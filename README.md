import random

class Move:
    def __init__(self, name, move_type, power, accuracy, category, effect=None):
        self.name = name
        self.type = move_type
        self.power = power
        self.accuracy = accuracy
        self.category = category
        self.effect = effect

    def apply_effect(self, target):
        if self.effect:
            self.effect(target)

class Item:
    def __init__(self, name, item_type, effect):
        self.name = name
        self.type = item_type
        self.effect = effect

    def use(self, target):
        self.effect(target)

class Pokemon:
    def __init__(self, name, poke_type, health, attack, defense, special_attack, special_defense, speed, level, experience, moves, is_wild=True):
        self.name = name
        self.type = poke_type
        self.health = health
        self.max_health = health
        self.attack = attack
        self.defense = defense
        self.special_attack = special_attack
        self.special_defense = special_defense
        self.speed = speed
        self.level = level
        self.experience = experience
        self.moves = moves
        self.is_wild = is_wild

    def attack_opponent(self, move, opponent):
        if random.random() <= move.accuracy:
            damage = self.calculate_damage(move, opponent)
            opponent.take_damage(damage)
        else:
            print(f"{self.name}'s attack missed!")

    def calculate_damage(self, move, opponent):
        # Simplified damage formula
        if move.category == 'physical':
            return (self.attack / opponent.defense) * move.power
        elif move.category == 'special':
            return (self.special_attack / opponent.special_defense) * move.power

    def take_damage(self, damage):
        self.health -= damage
        if self.health <= 0:
            self.health = 0
            print(f"{self.name} fainted!")

    def gain_experience(self, exp):
        self.experience += exp
        if self.experience >= self.level * 10:  # Simplified leveling system
            self.level_up()

    def level_up(self):
        self.level += 1
        self.health += 10
        self.max_health += 10
        self.attack += 5
        self.defense += 5
        self.special_attack += 5
        self.special_defense += 5
        self.speed += 5
        print(f"{self.name} leveled up to {self.level}!")

    def learn_move(self, move):
        if len(self.moves) < 4:
            self.moves.append(move)
        else:
            print(f"{self.name} already knows 4 moves!")

    def get_stat(self, stat):
        return getattr(self, stat)

class Trainer:
    def __init__(self, name):
        self.name = name
        self.team = []
        self.inventory = []

    def catch_pokemon(self, wild_pokemon, pokeball):
        if random.random() <= pokeball.catch_rate:
            self.team.append(wild_pokemon)
            wild_pokemon.is_wild = False
            print(f"{self.name} caught {wild_pokemon.name}!")
        else:
            print(f"{wild_pokemon.name} escaped!")

    def use_item(self, item, target):
        item.use(target)

    def battle(self, opponent):
        while any(p.health > 0 for p in self.team) and any(p.health > 0 for p in opponent.team):
            # Simplified turn-based battle system
            my_pokemon = next(p for p in self.team if p.health > 0)
            opp_pokemon = next(p for p in opponent.team if p.health > 0)

            if my_pokemon.speed >= opp_pokemon.speed:
                my_pokemon.attack_opponent(my_pokemon.moves[0], opp_pokemon)
                if opp_pokemon.health > 0:
                    opp_pokemon.attack_opponent(opp_pokemon.moves[0], my_pokemon)
            else:
                opp_pokemon.attack_opponent(opp_pokemon.moves[0], my_pokemon)
                if my_pokemon.health > 0:
                    my_pokemon.attack_opponent(my_pokemon.moves[0], opp_pokemon)

class Pokeball(Item):
    def __init__(self, name, catch_rate):
        super().__init__(name, 'Pokeball', None)
        self.catch_rate = catch_rate

# Example usage
move1 = Move("Tackle", "Normal", 40, 1.0, "physical")
move2 = Move("Ember", "Fire", 40, 1.0, "special")

charmander = Pokemon("Charmander", "Fire", 39, 52, 43, 60, 50, 65, 5, 0, [move1, move2])

trainer = Trainer("Ash")
wild_pokemon = Pokemon("Pidgey", "Normal", 40, 45, 40, 35, 35, 56, 3, 0, [move1], True)
pokeball = Pokeball("Pokeball", 0.5)

trainer.catch_pokemon(wild_pokemon, pokeball)
trainer.battle(Trainer("Gary"))
