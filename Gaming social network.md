title: Gaming Social Network
categories: 
    - Project
    - Intro to Computer Science
tags: [Dictionary,List,Recursion]
date: 2016-03-10
---
<img src="https://farm2.staticflickr.com/1647/25913549645_4dadc3365c_h.jpg" width="640" height="360">
### Introduction
> <font size=2>A social network game is a type of online game that is played through social networks, and typically features multiplayer and asynchronous gameplay mechanics. - [wikipedia](https://en.wikipedia.org/wiki/Social_network_game)</font>

The main issue about social network game is to store relationship between players and their properties with some data structures. Finally creating a network about these players.
<!--more-->
----       


### Dictionary and List
Dictionary and List are the most common used data structures in python for storing data.  
Dictionary add elements by assignment method and remove by pop method. For searching values, Dictionary use keys to get mathched one.
> <font size=2>A dictionary in Python is a collection of unordered values accessed by key rather than by index. The keys have to be hashable: integers, floating point numbers, strings, tuples, and frozensets are hashable, while lists, dictionaries, and sets other than frozensets are not. - [wikibooks](https://en.wikibooks.org/wiki/Python_Programming/Dictionaries)</font>  

List add elements by append method and remove by pop method. For searching values, List use subscript numbers starts from 0 to traverse.
> <font size=2>A list in Python is an ordered group of items (or elements). It is a very general structure, and list elements don't have to be of the same type: you can put numbers, letters, strings and nested lists all on the same list. - [wikibooks](https://en.wikibooks.org/wiki/Python_Programming/Lists)</font>


### Recursion
> <font size=2>In mathematics and computer science, a class of objects or methods exhibit recursive behavior when they can be defined by two properties:  
 1. A simple base case (or cases)—a terminating scenario that does not use recursion to produce an answer  
 2. A set of rules that reduce all other cases toward the base case  
-[wikipedia](https://en.wikipedia.org/wiki/Recursion#Formal_definitions)</font>  
  
---
### Project
For this project, we use Dictionary to realize social network, keys are player's name, and values are nested Lists, the outter List have two elements. The first element is a List containing all the players who have relationship with its keys, the second element is a List containing all the properties about the keys.  

The goal of this project is to implement the procedures according to the specifications below:
```python
#   Parses a block of text (such as the one above) and stores relevant information into a data structure. You are free to choose and design any data structure you would like to use to manage the information.
def create_data_structure(string_input):
#   Returns a list of all the connections that user has
def get_connections(network, user):
#   Returns a list of all the games a user likes
def get_games_liked(network,user):
#   Adds a connection from user_A to user_B. Make sure to check that both users exist in network.
def add_connection(network, user_A, user_B):
#   Creates a new user profile and adds that user to the network, along with any game preferences specified in games. Assume that the user has no connections to begin with.
def add_new_user(network, user, games):
#   Finds all the secondary connections (i.e. connections of connections) of a given user.
def get_secondary_connections(network, user):
#   Finds the number of people that user_A and user_B have in common.
def count_common_connections(network, user_A, user_B):
#   Finds a connections path from user_A to user_B. It has to be an existing path but it DOES NOT have to be the shortest path.
def find_path_to_friend(network,user_A,user_B):
```

> **Note:**
>  1. There could be some people don't have connections or don't have any properties. So I use an empty List to represent that case.
>  2. When dealing with get and add procedures, we should be careful that the given arguments could not be in the network, which should be return a None
> 3. When finding a path between two players, we use a DFS algorithm to find a not optimal but complete solution. Also we should mark the visited element in case we trap into a dead loop.

- [github code link](https://github.com/xiaofeixiawang/cs101/blob/master/final%20project.py)

---
