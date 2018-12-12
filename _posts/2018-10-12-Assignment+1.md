
---
title: Working with text in Python
---

# Assignment 1 - Creating and Manipulating Graphs

Eight employees at a small company were asked to choose 3 movies that they would most enjoy watching for the upcoming company movie night. These choices are stored in the file `Employee_Movie_Choices.txt`.

A second file, `Employee_Relationships.txt`, has data on the relationships between different coworkers. 

The relationship score has value of `-100` (Enemies) to `+100` (Best Friends). A value of zero means the two employees haven't interacted or are indifferent.

Both files are tab delimited.


```python
import networkx as nx
import pandas as pd
import numpy as np
from networkx.algorithms import bipartite


# This is the set of employees
employees = set(['Pablo',
                 'Lee',
                 'Georgia',
                 'Vincent',
                 'Andy',
                 'Frida',
                 'Joan',
                 'Claude'])

# This is the set of movies
movies = set(['The Shawshank Redemption',
              'Forrest Gump',
              'The Matrix',
              'Anaconda',
              'The Social Network',
              'The Godfather',
              'Monty Python and the Holy Grail',
              'Snakes on a Plane',
              'Kung Fu Panda',
              'The Dark Knight',
              'Mean Girls'])


# you can use the following function to plot graphs
# make sure to comment it out before submitting to the autograder
def plot_graph(G, weight_name=None):
    '''
    G: a networkx G
    weight_name: name of the attribute for plotting edge weights (if G is weighted)
    '''
    %matplotlib notebook
    import matplotlib.pyplot as plt
    
    plt.figure()
    pos = nx.spring_layout(G)
    edges = G.edges()
    weights = None
    
    if weight_name:
        weights = [int(G[u][v][weight_name]) for u,v in edges]
        labels = nx.get_edge_attributes(G,weight_name)
        nx.draw_networkx_edge_labels(G,pos,edge_labels=labels)
        nx.draw_networkx(G, pos, edges=edges, width=weights);
    else:
        nx.draw_networkx(G, pos, edges=edges);
```

### Question 1

Using NetworkX, load in the bipartite graph from `Employee_Movie_Choices.txt` and return that graph.

*This function should return a networkx graph with 19 nodes and 24 edges*


```python
def answer_one():
    import networkx as nx
    #nx.Graph('Employee_Movie_Choices.txt')
    ##nx.read_edgelist('Employee_Movie_Choices.txt',data=[('Movie',str)],create_using=nx.MultiDiGraph())# Your Code Here
    movie_choices = nx.read_edgelist('Employee_Movie_Choices.txt', delimiter='\t',create_using=nx.Graph())
                                     
    return movie_choices #nx.draw_networkx(movie_choices)#nx.Graph(movie_choices)# Your Answer Here
answer_one()
```




    <networkx.classes.graph.Graph at 0x7f72cc51c518>



### Question 2

Using the graph from the previous question, add nodes attributes named `'type'` where movies have the value `'movie'` and employees have the value `'employee'` and return that graph.

*This function should return a networkx graph with node attributes `{'type': 'movie'}` or `{'type': 'employee'}`*


```python
def answer_two():
    B = answer_one()
    for node in B.nodes():
        if node in employees:
            B.add_node(node, type='employee')
        else:
            B.add_node(node, type='movie') 
    return B# Your Answer Here
answer_two()
```




    <networkx.classes.graph.Graph at 0x7f72cc5216a0>



### Question 3

Find a weighted projection of the graph from `answer_two` which tells us how many movies different pairs of employees have in common.

*This function should return a weighted projected graph.*


```python
def answer_three():
    B=answer_two()
    X=employees
    Y=movies
    P=bipartite.weighted_projected_graph(B,X)
    
    return P# Your Answer Here
#plot_graph(answer_three())
```

### Question 4

Suppose you'd like to find out if people that have a high relationship score also like the same types of movies.

Find the Pearson correlation ( using `DataFrame.corr()` ) between employee relationship scores and the number of movies they have in common. If two employees have no movies in common it should be treated as a 0, not a missing value, and should be included in the correlation calculation.

*This function should return a float.*


```python
def answer_four():
    df_rlt=pd.read_csv('Employee_Relationships.txt',delimiter='\t',header=None)
    df_rlt['unic']=df_rlt[0]+df_rlt[1]
    df_rlt['unic']=df_rlt['unic'].map(lambda x:''.join(sorted(set(x))))
    df_rlt.set_index('unic',inplace=True)
    
    C=answer_three()
    df_cmon=pd.DataFrame(C.edges(data=True))
    df_cmon[2]=df_cmon[2].map(lambda x:x['weight'])
    df_cmon['unic']=df_cmon[0]+df_cmon[1]
    df_cmon['unic']=df_cmon['unic'].map(lambda x:''.join(sorted(set(x))))
    df_cmon.set_index('unic',inplace=True)
    #df_cmon.rename(columns={2:'cmon_mvies'},inplace=True)
    join_df=df_cmon.join(df_rlt,how='outer',rsuffix='_r').fillna(0)
    return join_df['2'].corr(join_df['2_r'])
answer_four()
```




    0.78839622217334759




```python
import networkx as nx

G=nx.MultiGraph()
G.add_node('A',role='manager')
G.add_edge('A','B',relation = 'friend')
G.add_edge('A','C', relation = 'business partner')
G.add_edge('A','B', relation = 'classmate')
#G.node['A']['role'] = 'team member'
G.node['B']['role'] = 'engineer'
G.nodes(data=True)
```




    [('A', {'role': 'manager'}), ('B', {'role': 'engineer'}), ('C', {})]




```python

```
