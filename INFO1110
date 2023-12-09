from os import kill
import sys
import re
import copy

#Classes 
class Player: 
    def __init__(self, base):
        self.base = base 
        self.soldiers = []
        self.wood = 2
        self.food = 2
        self.gold = 2

class Soldiers: 
    def __init__(self, wood, food, gold, type):
        self.wood = wood 
        self.food = food
        self.gold = gold
        self.type = type
        self.position1 = []
        self.position2 = []
    
    #This function returns the correct data of soldiers for the current player
    def position(self, player):
        if player == player1:
            return self.position1
        elif player == player2:
            return self.position2
    
    #Defines __str__ method in order to return the name of the instance as a string
    def __str__(self):
        if self.type == "S": 
            return "Spearman"
        elif self.type == "A": 
            return "Archer"
        elif self.type == "K": 
            return "Knight"
        elif self.type == "T": 
            return "Scout"
    
    #Defines equivalent and has properties in order to use classes in a dictionary to reorder them 
    def __eq__(self, other):
        return (self.type) == other.type
    
    def __hash__(self):
        return hash(self.type)

#Functions
def load_config_file(filepath):

    #Variable initialization
    width, height = 0, 0
    waters, woods, foods, golds = [], [], [], []
    water_coords, wood_coords, food_coords, gold_coords = [], [], [], []

    #Tries to open and read given file path lines: 
    fle = open(filepath, "r")
    data = fle.readlines()
    
    #Checks if the corret amount of lines are given
    if len(data) != 5: 
        raise SyntaxError("Invalid Configuration File: format error!")

    #Iterate through the data and store the relevant data:
    for count, line in enumerate(data): 
        if count == 0: 
            in_correct_format = re.search("^Frame:", line)
            if not in_correct_format: 
                raise SyntaxError("Invalid Configuration File: format error!")

            elif len(line) != 11: 
                raise SyntaxError("Invalid Configuration File: frame should be in format widthxheight!")
            
            #Error checking for format of frame widthxheight: 
            elif not line[7].isdigit() or not line[9].isdigit() or line[8] != "x": 
                raise SyntaxError("Invalid Configuration File: frame should be in format widthxheight!")
            
            #Error checking for the values of width and height: 
            elif line[7] not in ["5","6","7"] or line[9] not in ["5","6","7"]:
                raise ArithmeticError("Invalid Configuration File: width and height should range from 5 to 7!")
        
            width, height = int(line[7]), int(line[9])

        #Reads all the integers on the line and saves it accordingly. 
        elif count == 1: 
            in_correct_format = re.search("^Water:", line)
            if not in_correct_format: 
                raise SyntaxError("Invalid Configuration File: format error!")
            waters = line[7:len(waters)-1].split(" ")
        elif count == 2: 
            in_correct_format = re.search("^Wood:", line)
            if not in_correct_format: 
                raise SyntaxError("Invalid Configuration File: format error!")
            woods = line[6:len(woods)-1].split(" ")
        elif count == 3: 
            in_correct_format = re.search("^Food:", line)
            if not in_correct_format: 
                raise SyntaxError("Invalid Configuration File: format error!")
            foods = line[6:len(foods)-1].split(" ")  
        elif count == 4:
            in_correct_format = re.search("^Gold:", line)
            if not in_correct_format: 
                raise SyntaxError("Invalid Configuration File: format error!")
            golds = line[6:].split(" ") 
    
    #Closes the file to save OS memory space 
    fle.close()  

    #Tries to convert the data into integers, if unsuccessful, the error will be caught by other code
    try: 
        waters = list(map(int, waters))
        woods = list(map(int, woods))
        foods = list(map(int, foods))
        golds = list(map(int, golds))
    except: pass

    #Store the data into coordinates (x,y):
    for i in range(0, len(waters), 2):
        water_coords.append(tuple(waters[i:i+2]))
    for i in range(0, len(woods), 2):
        wood_coords.append(tuple(woods[i:i+2]))
    for i in range(0, len(foods), 2):
        food_coords.append(tuple(foods[i:i+2]))
    for i in range(0, len(golds), 2):
        gold_coords.append(tuple(golds[i:i+2]))

    #Check if there are any non-intenger characters and if conversion to int was successful: 
    if any(not isinstance(element, int) for element in waters):
        raise ValueError("Invalid Configuration File: Water contains non integer characters!")
    elif any(not isinstance(element, int) for element in woods): 
        raise ValueError("Invalid Configuration File: Wood contains non integer characters!")
    elif any(not isinstance(element, int) for element in foods): 
        raise ValueError("Invalid Configuration File: Food contains non integer characters!")
    elif any(not isinstance(element, int) for element in golds): 
        raise ValueError("Invalid Configuration File: Gold contains non integer characters!")
        
    #Check if any of the lines have an odd number of coordinates
    if len(waters) % 2 != 0:
        raise SyntaxError("Invalid Configuration File: Water has an odd number of elements!")
    elif len(woods) % 2 != 0:
        raise SyntaxError("Invalid Configuration File: Wood has an odd number of elements!")
    elif len(foods) % 2 != 0:
        raise SyntaxError("Invalid Configuration File: Food has an odd number of elements!")
    elif len(golds) % 2 != 0:
        raise SyntaxError("Invalid Configuration File: Gold has an odd number of elements!")
    
    #Check if the coords of materials are in the map
    x_coords = [i for i in range(int(width))]
    y_coords = [i for i in range(int(height))]
    
    if (any((element[0] not in x_coords) 
    or (element[1] not in y_coords) for element in water_coords)): 
        raise ArithmeticError("Invalid Configuration File: Water contains a position that is out of map.")

    elif (any((element[0] not in x_coords) 
    or (element[1] not in y_coords) for element in wood_coords)): 
        raise ArithmeticError("Invalid Configuration File: Wood contains a position that is out of map.")

    elif (any((element[0] not in x_coords) 
    or (element[1] not in y_coords) for element in food_coords)): 
        raise ArithmeticError("Invalid Configuration File: Food contains a position that is out of map.")

    elif (any((element[0] not in x_coords) 
    or (element[1] not in y_coords) for element in gold_coords)): 
        raise ArithmeticError("Invalid Configuration File: Gold contains a position that is out of map.")
    
    #Check if special positions are occupied by resources 
    special_pos = [
    (1,1), (0,1), (1,0), (2,1), (1,2), 
    (width-2, height-2), (width-3,height-2), 
    (width-2,height-3), (width-1,height-2), 
    (width-2,height-1)]

    lst_of_coords = water_coords + food_coords + wood_coords + gold_coords
    if any(element in special_pos for element in lst_of_coords):
        raise ValueError("Invalid Configuration File: The positions of home bases or the base positions next to the home bases are occupied!")

    #Check for duplicate position: 
    non_duplicate_list = set(lst_of_coords)
    if len(non_duplicate_list) != len(lst_of_coords): 
        for i in non_duplicate_list: 
            lst_of_coords.remove(i)
        duplicate_value = lst_of_coords
        raise SyntaxError(f"Invalid Configuration File: Duplicate position {duplicate_value[0]}!")

    #Success! 
    return width, height, water_coords, wood_coords, food_coords, gold_coords, special_pos

#Loads/Updates the coordinates on the map. 
#This is done by interating through every coordinate and checking what list that coordinate is in 
def load_coords(x,y): 
    home_bases = [player1.base, player2.base]
        
    for water in waters: 
        if (x,y) == water: 
            return "~~"
    for wood in woods:
        if (x,y) == wood: 
            return "WW" 
    for food in foods: 
        if (x,y) == food: 
            return "FF"
    for gold in golds: 
        if (x,y) == gold: 
            return "GG"
    for count, home in enumerate(home_bases): 
        if (x,y) == home: 
            return f"H{count+1}"
    for soldier in player1.soldiers:
        for pos in soldier.position1: 
            if (x,y) == pos: 
                return f"{soldier.type}1"
    for soldier in player2.soldiers:
        for pos in soldier.position2: 
            if (x,y) == pos: 
                return f"{soldier.type}2"
    
    return "  "

#Loads the structure of the map 
def load_map(width, height): 
    print("Please check the battlefield, commander.")
    #Print X-coordinates of map 
    print("  X", end="")
    for x in range(int(width)):
        #Removes extra spacing at the end of X 
        print(f"0{x}{'' if x == (int(width)-1) else ' '}", end="")
    print("X")

    #Print the layout of the map 
    print(" Y+",end="")
    for x in range(int(width)):
        #Removes extra hyphen at the end  
        print(f"--{'' if x == (int(width)-1) else '-'}", end="")
    print("+")

    #Prints the symbols on the map to represent each object
    for y in range(height):
        print(f"0{y}|",end="")
        for x in range(width):
            print(f"{load_coords(x,y)}|",end="" if x != int(width)-1 else "\n")
    
    #Print the layout of the map 
    print(" Y+",end="")
    for x in range(int(width)):
        #Removes extra hyphen at the end  
        print(f"--{'' if x == (int(width)-1) else '-'}", end="")
    print("+")

#Accounts for user commands such as QUIT, DIS, and PRIS en
def user_commands(user_input): 
    if user_input == "QUIT":
        exit()
    elif user_input == "DIS":
        return load_map(width, height)
    elif user_input == "PRIS":
        print("""Recruit Prices:
  Spearman (S) - 1W, 1F
  Archer (A) - 1W, 1G
  Knight (K) - 1F, 1G
  Scout (T) - 1W, 1F, 1G""")
    elif user_input == "NO": 
        return False
        
if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python3 little_battle.py <filepath>")
        sys.exit()
    width, height, waters, woods, foods, golds, special_pos = load_config_file(sys.argv[1])
    print(f"Configuration file {sys.argv[1]} was loaded.")

    #Soldier instances 
    spearman = Soldiers(1,1,0, "S")
    archer = Soldiers(1,0,1, "A")
    knight = Soldiers(0,1,1, "K")
    scout = Soldiers(1,1,1, "T")

    #Player instances 
    player1 = Player((1,1))
    player2 = Player((width-2, height-2))

    #Public Variables 
    year = 616
    player = player2
    is_new_turn = True
    is_recruiting_stage = True
    soldier_types = [spearman.type, archer.type, knight.type, scout.type]
    commands = ["QUIT", "DIS", "PRIS", "NO"]
    original_pos = 0
    scout_pos = (100,100)
    occupied_positions = []
    prev_scout_pos = (100,100)
    all_coords = []
    for x in range(width):
        for y in range(height):
            all_coords.append((x,y))

    #Little battle intro/tutorial: 
    print("Game Started: Little Battle! (enter QUIT to quit the game)\n")
    load_map(width, height)
    print("(enter DIS to display the map)\n")
    print("""Recruit Prices:
  Spearman (S) - 1W, 1F
  Archer (A) - 1W, 1G
  Knight (K) - 1F, 1G
  Scout (T) - 1W, 1F, 1G""")
    print("(enter PRIS to display the price list)\n")

    while True: 
        
        #Begins the recruiting stage 
        while is_recruiting_stage: 
            #Checks if it's a new turn and prints out the intro again if it is, and adds year if necessary 
            if is_new_turn: 
                if player == player1: 
                    player = player2
                    t = 2 
                elif player == player2: 
                    player = player1
                    t = 1
                    year += 1
                print(f"-Year {year}-\n")
                print(f"+++Player {t}'s Stage: Recruit Armies+++\n")
                print(f"[Your Asset: Wood - {player.wood} Food - {player.food} Gold - {player.gold}]")

                #Sets up the flow of the code so that each section runs when it's suppose to 
                is_new_turn = False
                is_recruiting_stage = True
                is_recruiting = True
                is_moving_stage = False
                is_moving = True

            #If the player does not have any resources, prompt them and move on to the next stage
            case1 = (player.wood, player.food, player.gold) == (0,0,0)
            case2 = (player.wood, player.food, player.gold) == (0,0,player.gold)
            case3 = (player.wood, player.food, player.gold) == (player.wood,0,0)
            case4 = (player.wood, player.food, player.gold) == (0,player.food,0)
            if case1 or case2 or case3 or case4:
                print("No resources to recruit any armies.")
                is_recruiting_stage = False
                is_moving_stage = not is_recruiting_stage
                break

            #Decide which player's surroundings to check 
            if player == player1: 
                home_base_surroundings = [(0,1), (1,0), (2,1), (1,2)]
            elif player == player2: 
                home_base_surroundings = [(width-3, height-2), (width-2, height-3), (width-1, height-2), (width-2,height-1)]

            #Save all occupied positions into one list and home base coords
            #This lists refereshes every turn 
            occupied_positions = [(1,1), (width-2, height-2)]
            for soldier1 in player1.soldiers: 
                for pos1 in soldier1.position(player1):
                    occupied_positions.append(pos1)
            for soldier2 in player2.soldiers: 
                for pos2 in soldier2.position(player2):
                    occupied_positions.append(pos2)

            #Checks if there are no places to recruit armies, if there isn't, then it ends the recruiting stage. 
            if set(home_base_surroundings).issubset(set(occupied_positions)):
                print("No place to recruit new armies.")
                is_recruiting_stage = False
                is_moving_stage = True
                break
            

            user_input = input("\nWhich type of army to recruit, (enter) ‘S’, ‘A’, ‘K’, or ‘T’? Enter ‘NO’ to end this stage.\n")
            if user_input in soldier_types: 

                    #Saves the soldier instance the player requested for 
                    if user_input == spearman.type: 
                        soldier_recruited = spearman
                    elif user_input == archer.type:
                        soldier_recruited = archer 
                    elif user_input == knight.type:
                        soldier_recruited = knight 
                    elif user_input == scout.type:
                        soldier_recruited = scout
    
                    #Check if player has sufficient resources
                    not_sufficient_resources = (
                        player.wood < soldier_recruited.wood
                    or player.food < soldier_recruited.food
                    or player.gold < soldier_recruited.gold)

                    #Check if the player has sufficient resources to recruit the requested soldier
                    while not_sufficient_resources:
                        print("Insufficient resources. Try again.")
                        test2_pass = False
                        is_recruiting = False
                        break
                    #If they do, mark the test as passed
                    else: 
                        test2_pass = True
                        is_recruiting = True

                    #Asks the user to pick a place to recruit 
                    while is_recruiting:
                        recruit_pos = input(f"\nYou want to recruit a {soldier_recruited.__str__()}. Enter two integers as format ‘x y’ to place your army.\n")

                        #Allows the user to call commands such as DIS... 
                        while recruit_pos in commands:
                            user_commands(recruit_pos)
                            if recruit_pos == "NO":
                                is_recruiting_stage = user_commands(user_input)
                                is_moving_stage = not is_recruiting_stage
                                is_recruiting = False
                                break
                            recruit_pos = input(f"\nYou want to recruit a {soldier_recruited.__str__()}. Enter two integers as format ‘x y’ to place your army.\n")

                        #Checks if it's in correct format 
                        #REGEX FORMAT: Must begin with integer, followed by space, followed by integer. 
                        #Must not having anything else i.e. no extra spaces or anything 
                        in_correct_format = re.search("^-?\d+\s\-?\d+$", recruit_pos)
                        if in_correct_format:

                            #Make sure the input position is interperted as a tuple
                            if not isinstance(recruit_pos, tuple): 
                                recruit_pos = recruit_pos.split(" ")
                                recruit_pos = list(map(int, recruit_pos))
                                recruit_pos = (int(recruit_pos[0]), int(recruit_pos[1]))
                            
                            #Check if the requested position is available 
                            not_free_pos = recruit_pos in occupied_positions or recruit_pos not in home_base_surroundings
                            
                            #If it's not available, then prompt the user and ask them to try again 
                            while not_free_pos:
                                print("You must place your newly recruited unit in an unoccupied position next to your home base. Try again.")
                                test1_pass = False
                                break

                            #if it is available, mark this test as passed
                            else: 
                                test1_pass = True 
                            
                            #If both tests passed, then:
                            #take away resources from player based on which soldier was recruited 
                            if test1_pass and test2_pass: 

                                player.wood -= soldier_recruited.wood
                                player.food -= soldier_recruited.food
                                player.gold -= soldier_recruited.gold

                                #Add the recruited soldier's object into player object
                                soldier_recruited.position(player).append(recruit_pos)
                                player.soldiers.append(soldier_recruited)
                                print(f"\nYou has recruited a {soldier_recruited}.")
                                print(f"\n[Your Asset: Wood - {player.wood} Food - {player.food} Gold - {player.gold}]")
                                break
                        else: 
                            print("Sorry, invalid input. Try again.")
            
            #If the command is neither an edge case or a a soldier type, then prompt the user with an error message
            elif user_input not in commands and user_input not in soldier_types: 
                print("Sorry, invalid input. Try again.")   
        
            #If the input is a command, then allow the user to 
            elif user_input in commands:
                user_commands(user_input)
                if user_input == "NO":
                    is_recruiting_stage = False
                    is_moving_stage = not is_recruiting_stage

        #Gets a deep copy of the player's soldiers list in order to limit each soldier's moves
        #The set conversion is just to remove any duplicates 
        player.soldiers = list(set(player.soldiers))
        army_to_move = copy.deepcopy(player.soldiers)
        print(f"\n===Player {t}'s Stage: Move Armies===")
        
        #Moving stage: 
        while is_moving_stage: 

            #If the player has no more soldiers to move, then end the turn. 
            if army_to_move == []: 
                #Tell the user that it's a new turn, and go to the recruiting stage
                is_new_turn = True
                is_recruiting_stage = True
                is_moving_stage = False
                print("\nNo Army to Move: next turn.\n")
                break

            else:  
                print("\nArmies to Move:")

                #Reorders the list by attaching a number to each object in the list. 
                order = {spearman: 1, archer: 2, knight: 3, scout: 4}
                #Iterates through the list (x), and reorders it in the order of order[x] (i.e. the int attached in dict)
                army_to_move = sorted(army_to_move, key=lambda x: order[x])
                
                #Prints all the soldiers that the player has and their respective locations 
                for soldier in army_to_move: 
                    print(f"  {soldier}: ",end="")
                    for count, pos in enumerate(soldier.position(player)):
                        #If at the end of the line, then don't print out a comma, instead go to the next line
                        print(pos,end=", " if count != len(soldier.position(player))-1 else "\n")
                
                is_moving = True
            
            #Asks the player to request a new position 
            while is_moving: 
                user_input = input("\nEnter four integers as a format ‘x0 y0 x1 y1’ to represent move unit from (x0, y0) to (x1, y1) or ‘NO’ to end this turn.\n")

                #Accounts for user commands 
                while user_input in commands:
                    user_commands(user_input)
                    is_moving = False
                    if user_input == "NO":
                        is_moving_stage = False
                        is_recruiting_stage = not is_moving_stage
                        is_moving = False
                        is_new_turn = True
                    break

                #Checks for correct format
                #REGEX: Must start with int, and must follow pattern: (int int int int) with nothing extra. 
                in_correct_format = re.search("^-?\d+\s-?\d+\s-?\d+\s-?\d+$", user_input)

                #Saves the soldiers that were moves to ensure that they can't be moved from the 'back-end' 

                #iterates through the soldiers that are to move this turn and saves their positions in a list
                lst = []
                for armyT in army_to_move:
                    for posT in armyT.position(player):
                        lst.append(posT)

                #Checks if the location of the soldier is not in army_to_move, if not, then that means the soldier already moved 
                already_moved = []
                for armyS in player.soldiers:
                    for posS in armyS.position(player):
                        if posS not in lst:
                            already_moved.append(posS)

                if in_correct_format:
                    #Ensures the user input is interpeted as a tuple
                    #There are too many conditions where this applies and too many conditions where this doesn't apply 
                    #Hence the try and pass (This only works at the beginning of the loop because it's not a tuple at the beginning) 
                    try:
                        original_pos = (int(user_input[0]), int(user_input[2]))
                        new_pos = (int(user_input[4]), int(user_input[6]))
                    except: pass
                    
                    #Checks if the requested soldier has already moved or not 
                    if original_pos in already_moved:
                        print("Invalid move. Try again.")
                        is_moving = False
                        break

                    no_position_found = True
                    is_valid_move = True
                    #Get the soldier that is in the requested position
                    for soldier in player.soldiers: 
                        for position in soldier.position(player):
                            if original_pos == position and is_valid_move:
                                no_position_found = False

                                #Define the soldier's allowed movements 
                                allowed_moves = [
                                    (original_pos[0]-1, original_pos[1]),
                                    (original_pos[0]+1, original_pos[1]),
                                    (original_pos[0], original_pos[1]-1),
                                    (original_pos[0], original_pos[1]+1) 
                                    ]
                                
                                allowed_moves_scout = [
                                    (original_pos[0]-2, original_pos[1]),
                                    (original_pos[0]+2, original_pos[1]),
                                    (original_pos[0], original_pos[1]-2),
                                    (original_pos[0], original_pos[1]+2) 
                                    ]
                                
                                #This defines where the player's soldiers are in order to ensure they can't attack their own soldiers
                                if player == player1: 
                                    player_soldiers = [(1,1)]
                                elif player == player2: 
                                    player_soldiers = [(width-2, height-2)]
                                
                                #Checks if the soldier can move to the new position   
                                if soldier == scout: 
                                    for pos in allowed_moves_scout:
                                        allowed_moves.append(pos) 
                                
                                #Ensures that the player can't attack their own soldiers
                                for soldierT in player.soldiers:
                                    for position in soldierT.position(player):
                                        player_soldiers.append(position)
                                
                                #Checks for invalid moves such as outside map, on own player, or on home bases
                                if (new_pos not in allowed_moves 
                                    or new_pos in player_soldiers 
                                    or new_pos not in all_coords):

                                    is_moving = False 
                                    is_valid_move = False
                                    print("Invalid move. Try again.")
                                    break

                                #If the requested position is not occupied, then check if there are any resources or water on that location
                                else:
                                    print(f"\nYou have moved {soldier} from {original_pos} to {new_pos}.")

                                    #Determine which base is the winning base this turn
                                    if player == player1: 
                                        winning_base = (width-2, height-2)
                                    elif player == player2: 
                                        winning_base = (1,1)
                                    
                                    #Checks if moving to winning base
                                    #If this was invalid, it would not have got to this point in the code
                                    #therefore, no extra conditions are needed
                                    if new_pos == winning_base:
                                        print(f"The army {soldier} captured the enemy’s capital.")
                                        name = input("\nWhat’s your name, commander?\n")
                                        print(f"\n***Congratulation! Emperor {name} unified the country in {year}.***")
                                        exit()


                                    kill_soldier = False
                                    #Accounts for what block the scout moved across.  
                                    if soldier == scout: 
                                        a = -1
                                        b = -1
                                        #Subtracts the new position from the original position in order to determine which axes the scout moved in
                                        scout_pos = tuple(map(lambda x, y: x - y, new_pos, original_pos))

                                        #If the soldier is moving forward (i.e. 1 to 3) then add 1 
                                        #to the axis in which the scout originalyl was 
                                        #this determines what block the scout jumped across 
                                        if original_pos[0] > new_pos[0]: a = 1 
                                        if original_pos[1] > new_pos[1]: b = 1

                                        if scout_pos[0] != 0:
                                            scout_pos = ((new_pos[0]+a), new_pos[1])
                                        elif scout_pos[1] != 0:
                                            scout_pos = ((new_pos[0], new_pos[1]+b))

                                        #Checks what position a scout jumped across first and collect resource/kill soldier accordingly
                                        if scout_pos in golds: 
                                            print("Good. We collected 2 Gold.")
                                            player.gold += 2 
                                            golds.remove(scout_pos)
                                        elif scout_pos in foods: 
                                            print("Good. We collected 2 Food.") 
                                            player.food += 2 
                                            foods.remove(scout_pos)
                                        elif scout_pos in woods: 
                                            print("Good. We collected 2 Wood.") 
                                            player.wood += 2
                                            woods.remove(scout_pos)
                                        elif scout_pos in waters:
                                            print(f"We lost the army {soldier.__str__()} due to your command!") 
                                            kill_soldier = True
                                            is_moving = False
                                        elif scout_pos == winning_base:
                                            print(f"The army {soldier} captured the enemy's capital.")
                                            name = input("\nWhat’s your name, commander?\n")
                                            print(f"\n***Congratulation! Emperor {name} unified the country in {year}.***")
                                            exit()
                                    
                                    #if the soldier has not been killed, then check if there are any resources where the soldier moved to
                                    if not kill_soldier:
                                        if new_pos in golds: 
                                            print("Good. We collected 2 Gold.")
                                            player.gold += 2 
                                            golds.remove(new_pos)
                                        elif new_pos in foods: 
                                            print("Good. We collected 2 Food.") 
                                            player.food += 2 
                                            foods.remove(new_pos)
                                        elif new_pos in woods: 
                                            print("Good. We collected 2 Wood.") 
                                            player.wood += 2
                                            woods.remove(new_pos)
                                        elif new_pos in waters:
                                            print(f"We lost the army {soldier.__str__()} due to your command!") 
                                            kill_soldier = True
                                            is_moving = False

                                    if player == player1:
                                        defending_player = player2
                                    elif player == player2:
                                        defending_player = player1
                                    
                                    #This accounts for what happens when a soldier attacks another soldier
                                    no_battle = True

                                    for defending_soldier in defending_player.soldiers:

                                        #Checks the position requested or scout position is occupied by an enemy soldier
                                        if((new_pos in defending_soldier.position(defending_player) 
                                            or scout_pos in defending_soldier.position(defending_player))
                                            and not kill_soldier): 

                                            #Determine what soldiers beat what soldiers 
                                            if soldier == spearman: weaker_soldiers = [knight, scout]
                                            elif soldier == archer: weaker_soldiers = [spearman, scout]
                                            elif soldier == knight: weaker_soldiers = [archer, scout]
                                            elif soldier == scout: weaker_soldiers = []

                                            #Accounts for what happens when a soldier battles the same type
                                            if defending_soldier == soldier: 
                                                no_battle = False

                                                #Removes soldier objects from player's data as it has been killed 
                                                soldier.position(player).remove(original_pos)

                                                #If the position that the soldier died in is not the new position requested, 
                                                try: defending_soldier.position(defending_player).remove(new_pos)
                                                #then it must be in between the new position and original position (i.e. scout jumped and died)
                                                except: defending_soldier.position(defending_player).remove(scout_pos)
                                                print(f"We destroyed the enemy {soldier} with massive loss!")

                                                #If there are no more soldiers of this type on the map, then remove the soldier instance 
                                                #from the player's data
                                                if defending_soldier.position(defending_player) == []:
                                                    defending_player.soldiers.remove(defending_soldier)
                                                if soldier.position(player) == []:
                                                    player.soldiers.remove(soldier)

                                            #Returns the result of the battle based on what soldiers are weaker (determined above)

                                            #If the enemy soldier is weaker than the current player's soldier, 
                                            elif defending_soldier in weaker_soldiers:
                                                for position in defending_soldier.position(defending_player):
                                                    #this condition ensures it's removing the correct soldier position on the map 
                                                    if position == new_pos:

                                                        #then remove the soldier from the enemy player's data 
                                                        print(f"Great! We defeated the enemy {defending_soldier}!") 
                                                        defending_soldier.position(defending_player).remove(position)
                                                        if defending_soldier.position(defending_player) == []:
                                                            defending_player.soldiers.remove(defending_soldier)

                                            #If not, then the soldier must be weaker, and hence remove the soldier from the current player's data 
                                            else: 
                                                for position in soldier.position(player):
                                                    if position == original_pos:
                                                        print(f"We lost the army {soldier} due to your command!")   
                                                        soldier.position(player).remove(position)
                                                        if soldier.position(player) == []:
                                                            player.soldiers.remove(soldier)
                                                no_battle = False

                                    #Limits each soldier to one move per turn
                                    #if the soldier has been killed, then remove them from the map and from the player's data
                                    if kill_soldier:
                                        for army in army_to_move:
                                            if army.__str__() == soldier.__str__():
                                                army.position(player).remove(original_pos)
                                                soldier.position(player).remove(original_pos)
                                                if army.position(player) == []:
                                                    army_to_move.remove(army)
                                                if soldier.position(player) == []:
                                                    player.soldiers.remove(soldier)
                                        original_pos = 0
                                        break

                                    #If the soldier has not been killed, then don't let that soldier move again this turn
                                    else:
                                        for army in army_to_move:
                                            if army.__str__() == soldier.__str__():
                                                army.position(player).remove(original_pos)
                                                if army.position(player) == []:
                                                    army_to_move.remove(army)
                                    
                                    #if the soldier didn't battle, then simply move the soldier to the new position
                                    if no_battle: 
                                        #this loop ensures the order of coordinates stays the same 
                                        for count, posB in enumerate(soldier.position(player)):
                                            if posB == original_pos: 
                                                soldier.position(player)[count] = new_pos
                                        #Changes the location of the friendly soldier positions 
                                        player_soldiers.append(new_pos)
                                        player_soldiers.remove(original_pos)

                                    #Ends this sub-stage
                                    is_moving = False
                                    prev_scout_pos = copy.copy(scout_pos)
                                    scout_pos = (100,100)

                                    #Sets original position back to 0 in order for the loop to run again if needed
                                    original_pos = 0
                                    break
                    if no_position_found:
                        print("Invalid move. Try again.")
                        is_moving = False
                elif user_input not in commands and not in_correct_format: 
                    print("Invalid move. Try again.")
                    is_moving = False
