#!/usr/bin/env python
# coding: utf-8

# ## Game Rules

# In[1]:


# Import the random library
import random
# Import the string library
import string

# Set the load_words function
def load_words():
  # Initialize the word_list variable
  word_list = open('words.txt', 'r').readline().split()
  # Return the word_list
  return word_list


# In[2]:


# Test the function
load_words()


# **Expected Output:**
# 
# `ad am an as at ax be by do em en ex go he hi ho if in is it me my no of...`

# ## Random Word Generator

# In[4]:


# Set the choose_word_random function
def choose_word_random(word_list):
  # Return the random word from the word_list
  return random.choice(word_list)


# In[5]:


# Local testing (not file-based words)
test_word_list = ['sun', 'sky', 'water', 'fire', 'nature']
print(choose_word_random(test_word_list))


# ## Guessed Word

# In[8]:


# Set the is_word_guessed function
def is_word_guessed(gameword, letters_already_guessed):
  # Set a condition
  if set(gameword) & set(letters_already_guessed) == set(gameword):
   # Return True
    return True
  else:
    # Return False
    return False


# In[9]:


# Local testing
test_gameword = 'sunny'
test_letters_already_guessed = ['s', 'u', 'n', 'y']
print(is_word_guessed(test_gameword, test_letters_already_guessed))


# **Expected Output:**
# 
# `True`

# ## State of Affairs

# In[14]:


# Set the get_guessed_word function
def get_guessed_word(gameword, letters_already_guessed):
  result_list = []
  # Create a for loop to iterate through game word
  for i in range(len(list(gameword))):
    # Set a condition
    if list(gameword)[i] in set(letters_already_guessed):
      # Append correct guessed letters to the result_list
      result_list.append(list(gameword)[i])
    else:
      # Append '_ ' to the result_list
      result_list.append('_ ')
  return ''.join(result_list)


# In[15]:


# Local testing
test_gameword = 'sunny'
test_letters_already_guessed = ['n']
print(get_guessed_word(test_gameword, test_letters_already_guessed))


# **Expected Output:**
# 
# `_ _ nn_`

# ## Available Letters

# In[20]:


# Set the get_available_letters function
def get_available_letters(letters_already_guessed):
  # Set the variable-alphabet
  available_list = list(string.ascii_lowercase)
  # Set a for loop
  for i in range(-len(available_list), 0):
    # Set a condition
    if available_list[i] in letters_already_guessed:
      # Delete an element from the available_list
      del available_list[i]
  # Appending a space
  return ''.join(available_list)


# In[21]:


# Local testing
test_letters_already_guessed = ['s', 'u', 'm', 'n', 'a']
print(get_available_letters(test_letters_already_guessed))


# **Expected Output:**
# 
# `bcdefghijklopqrtvwxyz`

# ## Hints 1/2

# In[24]:


# Set the hints_match function
def hints_match(word_to_match, word_from_list):
  # Remove spaces
  test_list = list(word_to_match.replace(' ', ''))
  other_list = list(word_from_list)
  #  Compare the lengths
  if len(test_list) != len(other_list):
    # Return False
    return False
  else:
    counter = 0
    # Set a for loop
    for i in range(len(test_list)):
      if test_list[i] == '_':
        if other_list[i] not in test_list:
          # Increment the counter
          counter += 1
      elif test_list[i] == other_list[i]:
        # Increment the counter
        counter += 1
  if counter == len(word_to_match.replace(' ', '')):
    return True
  else:
    return False


# In[25]:


# Local testing
test_word_to_match = 's_ _ _ _'
test_word_from_list = 'sunny'
print(hints_match(test_word_to_match, test_word_from_list))


# **Expected Output:**
# 
# `True`

# ## Hints 2/2

# In[26]:


# Set the show_possible_matches function
def show_possible_matches(word_to_match):
  possible_matches=[]
  # Set a for loop
  for word in test_word_list:
    if hints_match(word_to_match, word):
      # Append an element to the possible_matches list
      possible_matches.append(word)
  # Set a condition
  if len(possible_matches) == ():
    return 'No matches found'
  else:
    return ' '.join(possible_matches)


# In[27]:


# Local testing
test_word_list = ['sun', 'sky', 'water', 'fire', 'nature']
test_word_to_match = 's_ _ '
print(show_possible_matches(test_word_to_match))


# **Expected Output:**
# 
# `sun sky`

# ## Main Body

# In[38]:


def hangman(gameword):
    letters_guessed = []
    guesses_remaining = 6
    warnings_remaining = 3

    print("Welcome!\n"
          "A gameword is", len(gameword), "letters long:)", "\n")
    while True:
        # Check if the word has not been guessed
        if not is_word_guessed(gameword, letters_guessed):
            print("You have", int(guesses_remaining), "guess(es) left and", int(warnings_remaining), "warning(s) left!")
            # Display the available letters
            print("Available letters:", get_available_letters(letters_guessed))
            print("\nGame word:", get_guessed_word(gameword, letters_guessed))
            print('\n------------------------------------------------\n')

            guess = str.lower(input("Please, input a letter: "))

            # Check if the guess is not a valid letter
            if not guess.isalpha():
                if guess == "!":
                    print("Possible word matches are:",
                          show_possible_matches(get_guessed_word(gameword, letters_guessed)))
                else:
                    print("That is not a valid symbol.\n")

                    if warnings_remaining <= 0:
                        print("You have no warnings left so you lose 1 guess:_(")
                        guesses_remaining -= 1
                    else:
                      warnings_remaining -= 1
                      print("You loose 1 warning:_(")

            # Verify if the guessed letter has already been
            elif guess in set(letters_guessed):

                print("Oops! You've already guessed that letter.\n", end="")
                if warnings_remaining <= 0:
                    print("You have no warnings left so you lose 1 guess:_(")
                    guesses_remaining -= 1
                else:
                  warnings_remaining -= 1
                  print("You loose 1 warning:_(")
                press = input('Press any key to continue')

            # Determine if the guessed letter is present in the game word
            elif gameword:
                print("Super! Good guess!")
                letters_guessed.append(guess)
            else:
                letters_guessed.append(guess)
                print("Oops! That letter is not in my word.")
                print("You loose 1 guess :_(")
                guesses_remaining -= 1

            # Check if there no guesses remaining
            if guesses_remaining <= 0:
                print("Sorry, you ran out of guesses. The word was", gameword)
                break

        else:
            print("Congratulations, you won!\n")
            print("The gameword is: ", gameword)
            break


# ## Final Step

# In[39]:


# Execute the load_words
word_list = load_words()
# Set the gameword
gameword = choose_word_random(word_list)
# Execute the hangman function
hangman(gameword)

