# genetic-tree
implementation of a script in python that takes in input a file containing a list of mutations and a file containing a matrix of 0 and 1 associated to the species (rows) and generates a random phylogenetic tree
import sys
import random
import math
from anytree import Node, RenderTree
from deap import base , creator , tools


#Lettura del file matrix in input
file_rows=[]
with open(sys.argv[1],'r') as input_file:
        for s in input_file:
                file_rows.append(s.split())

list_tree={}
mutations=[]
#label_node={}
#dizionario={}
#Lettura del file mutations in input
with open(sys.argv[2], 'r') as input_file:
        for s in input_file:
                mutations.append(s.strip())
                #dizionario[random.randint(1,1000)]=s.strip()


def tree(root):
        d={}
        dizionario={}
        for i in mutations:
                dizionario[random.randint(1,1000)]=i
        keys_ordered=sorted(dizionario.keys())
        parent1=root
        #parent1=Node("root", parent=None)
        d[0]=parent1
        cont=0
        cont1=1
        for i in keys_ordered:
                if(cont<2):
                        parent2=Node(dizionario[i],parent=parent1)
                        cont=cont+1
                        d[cont1]=parent2
                if(cont==2):
                        cont=0
                        parent1=d[int(cont1/2)]
                cont1=cont1+1
        list_tree[root.name]=d
        print(RenderTree(d[0]))

def vista_albero():
        print(list_tree.keys())
        root=input('scegli il nome dell albero da visualizzare')
        return list_tree[root]



def attach_node(x):
        if(list_tree=={}):
                return print("PRIMA SI GENERI L ALBERO")
        tmp=vista_albero()
        print("key  node")
        for i in tmp.keys():
                print(i, tmp[i])
        new_node=input('seleziona la key del nodo padre')
#       new_m=input('inserisci il nome della nuova mutazione')
        if x=='3':
                new_m=input('inserisci la key della nuova mutazione')
                if (tmp[int(new_m)] in tmp[int(new_node)].ancestors or
                 tmp[int(new_m)]== tmp[int(new_node)]):
                        return print("non è possibile ,mutazione già presente")
                node=Node(tmp[int(new_m)].name, parent=tmp[int(new_node)])
        elif x=='5':
                new_m=input('inserisci la key della nuova mutazione')
                if(tmp[int(new_m)] in tmp[int(new_node)].ancestors or
                tmp[int(new_m)]==tmp[int(new_node)]):
                        return print("non è possibile,mutazione già presente")
                figli=tmp[int(new_node)].children
                node=Node(tmp[int(new_m)].name)
                tmp[int(new_node)].children=[node]
                for i in figli:
                        i.parent=node
        elif x=='2':
                mutation=input('inserisci la key della mutazione da eliminare')
                node=Node("-"+tmp[int(mutation)].name,parent=tmp[int(new_node)])
#               print(node.name)
        elif x=='6':
                mutation=input('inserire la key della mutazione da eliminare')
                figli=tmp[int(new_node)].children
                node=Node("-"+tmp[int(mutation)].name)
                tmp[int(new_node)].children=[node]
                for i in figli:
                        i.parent=node

        tmp[len(tmp)]=node


def path_mutation():
        #array=[]
        if list_tree=={}:
                return print("PRIMA SI GENERI L ALBERO")
        tmp=vista_albero()
        print("key  node")
        for i in tmp.keys():
                print(i, tmp[i])
        index_node=input("seleziona la key del nodo")
        return path(tmp[int(index_node)])


def path(node):
        array=[]
        antenati=node.ancestors
        m=[]
        m.append(node.name)
        for i in antenati:
                m.append(i.name)
        for j in mutations:
                if (j in m) and not("-"+j in m):
                        array.append(1)
                elif "-"+j in m and j in m:
                        array.append(0)
                elif "-"+j in m and not(j in m):
                        array.append(-1)
                else :
                        array.append(0)
        #print(mutations)
        #print(m)
        return array

alfa=0.15
beta=0.001
def evaluate(individual,name,tree):
        leafs=[]
        res={}
        for i in tree.values():
                if(i.is_leaf):
                        leafs.append(i)
        for leaf in leafs:
                likelihood=0
                i=path(leaf)
                #print(i)
                for j in range(len(individual)):
                        if individual[j] == '0':
                                if i[j] == 0:
                                        likelihood+=math.log10(1-beta)
                                elif i[j] == 1:
                                        likelihood+=math.log10(beta)
                                elif i[j] == -1:
                                        return print("error, perdita mutazione non esistente")
                        elif individual[j] == '1':
                                if i[j] == 0:
                                        likelihood+=math.log10(1-alfa)
                                elif i[j] == 1:
                                        likelihood+=math.log10(alfa)
                                elif i[j] == -1:
                                        return print("error perdita mutazione non esistente")
                        elif individual[j]== '2':
                                likelihood+=0
                res[likelihood]=leaf
##      if res.keys==[]:
##              return print("error", individual)
#       print(res.keys())
        a=max(res.keys())
##      print(risultato)
##      print(res[risultato])
        Node(name,parent=res[a])
        #return risultato

likelihood_tree={}
def val_likelihood():
        for i in likelihood_tree.keys():
                print(i,likelihood_tree[i])
        val=input('seleziona key')
        print(likelihood_tree[int(val)])
#parte di codice con la libreria deap a
while True:
        #control=input('seleziona un opzione : ')
        print("likelihood alberi            : 0")
        print("generare albero              : 1")
        print("eliminare nodo               : 2")
        print("inserire nodo                : 3")
        print("mostra lista degli alberi    : 4")
        print("inserisci un nodo intermedio : 5")
        print("elimina un nodo intermedio   : 6")
        print("mostra path array            : 7")
        print("valutare albero              : 8")
        print("exit                         : n")
        control=input('seleziona un opzione : ')
        if control=='n':
                break
        elif control=='1':
                name=input('inserisci il nome della radice')
                tree(Node(name,parent=None))
        elif control=='2':
                attach_node(control)
        elif control=='3':
                attach_node(control)
        elif control=='4':
                rst=vista_albero()
                print(RenderTree(rst[0]))
        elif control=='5':
                attach_node(control)
        elif control=='6':
                attach_node(control)
        elif control=='7':
                ris=path_mutation()
                print(ris)
        elif control=='8':
                tmp=vista_albero()
                for i in range(57):
                        evaluate(file_rows[i],i,tmp)
                        print(tmp[0].name)
        #       likelihood_tree[tmp[0].name]=likelihood
        elif control=='9':
                print(file_rows[11])
        elif control=='0':
                val_likelihood()
