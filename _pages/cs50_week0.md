---
title: "CS50-AI Week 0. Project 1: Degrees"
permalink: /_pages/cs50/week0-degrees
tags:
  - cs50
  - Python
toc: true
---

```python
import csv
import sys

from util import Node, StackFrontier, QueueFrontier

# Maps names to a set of corresponding person_ids
names = {}

# Maps person_ids to a dictionary of: name, birth, movies (a set of movie_ids)
people = {}

# Maps movie_ids to a dictionary of: title, year, stars (a set of person_ids)
movies = {}

 
def load_data(directory):
    """
    Load data from CSV files into memory.
    """
    # Load people
    with open(f"{directory}/people.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            people[row["id"]] = {
                "name": row["name"],
                "birth": row["birth"],
                "movies": set()
            }
            if row["name"].lower() not in names:
                names[row["name"].lower()] = {row["id"]}
            else:
                names[row["name"].lower()].add(row["id"])

    # Load movies
    with open(f"{directory}/movies.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            movies[row["id"]] = {
                "title": row["title"],
                "year": row["year"],
                "stars": set()
            }

    # Load stars
    with open(f"{directory}/stars.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            try:
                people[row["person_id"]]["movies"].add(row["movie_id"])
                movies[row["movie_id"]]["stars"].add(row["person_id"])
            except KeyError:
                pass


def main():
    if len(sys.argv) > 2:
        sys.exit("Usage: python degrees.py [directory]")
    directory = sys.argv[1] if len(sys.argv) == 2 else "large"

    
    # Load data from files into memory
    print("Loading data...")
    load_data(directory)
    print("Data loaded.")

    source = person_id_for_name(input("Name: "))
    if source is None:
        sys.exit("Person not found.")
    target = person_id_for_name(input("Name: "))
    if target is None:
        sys.exit("Person not found.")

    path = shortest_path(source, target)

    if path is None:
        print("Not connected.")
    else:
        degrees = len(path)
        print(f"{degrees} degrees of separation.")
        path = [(None, source)] + path
        for i in range(degrees):
            person1 = people[path[i][1]]["name"]
            person2 = people[path[i + 1][1]]["name"]
            movie = movies[path[i + 1][0]]["title"]
            print(f"{i + 1}: {person1} and {person2} starred in {movie}")


def shortest_path(source, target):
    """
    Returns the shortest list of (movie_id, person_id) pairs
    that connect the source to the target.
    If no possible path, returns None.
    """
    
    # Initialize queue
    queue = QueueFrontier()
    queue.add(Node(state=source, parent=None, action=None))
    
    # Initialize visited nodes
    exploredNode=set()
    
    # Initialize path
    path=set()
    
    found=False
    
    # While solution isn't found and the queue is not empty
    while not found or not queue.empty():
        
        # Choose a node from the frontier and remove explored
        current_node = queue.remove()
                
        # Add node.state (source person_id) to explored
        exploredNode.add(current_node.state)
        
        # if current_node.state not in explored.frontier:
        neighbors=neighbors_for_person(current_node.state)
        
        for movie_id, person_id in neighbors:
            # if person_id of node.state's neighbors is not in explored
            
            if not queue.contains_state(person_id) and person_id not in exploredNode:
                # state=next_person person_id
                next_person = Node(state=person_id, parent=current_node, action=movie_id)
                
                # if the next person_id is target
                if next_person.state == target:
                    path_movie_id = []
                    path_person_id = []
                    
                    #Trace the path backwards and then reverse
                    while next_person.parent is not None:
                        path_movie_id.append(next_person.action)
                        path_person_id.append(next_person.state)
                        next_person = next_person.parent
                        
                    path_movie_id.reverse()
                    path_person_id.reverse()
                    found=True
                    
                    path=list(zip(path_movie_id,path_person_id))
                    
                    return path
                else:
                    queue.add(next_person)
                    
    # If NO solution was found:
    if found==False:
        return None

def person_id_for_name(name):
    """
    Returns the IMDB id for a person's name,
    resolving ambiguities as needed.
    """
    person_ids = list(names.get(name.lower(), set()))
    if len(person_ids) == 0:
        return None
    elif len(person_ids) > 1:
        print(f"Which '{name}'?")
        for person_id in person_ids:
            person = people[person_id]
            name = person["name"]
            birth = person["birth"]
            print(f"ID: {person_id}, Name: {name}, Birth: {birth}")
        try:
            person_id = input("Intended Person ID: ")
            if person_id in person_ids:
                return person_id
        except ValueError:
            pass
        return None
    else:
        return person_ids[0]


def neighbors_for_person(person_id):
    """
    Returns (movie_id, person_id) pairs for people
    who starred with a given person.
    """
    movie_ids = people[person_id]["movies"]
    neighbors = set()
    for movie_id in movie_ids:
        for person_id in movies[movie_id]["stars"]:
            neighbors.add((movie_id, person_id))
    return neighbors


if __name__ == "__main__":
    main()
```
