def get_truth_table_asyn_mut(nodos, #lista de nombres de nodos p.e.: ['YAB','ARF3','miR','KAN','REV','AS12']
                             edo_inicial, #string con edos iniciales de todos los nodos p.e.: '111111'
                             pasos, #integer con max de pasos en el tiempo que se correra la tabla p.e.: 500
                             reglas, #string con reglas logicas de cada nodo separadas con ';' p.e.: '~AS12;~AS12;(~REV&AS12);YAB;~miR;(REV&~KAN)'
                             gen_mut, #lista de nodos mutados p.e.: ['YAB','miR']
                             edo_gen_mut, #lista de edos nodos mutados p.e.: ['0','0']
                             attrs_sincrona #lista con attrs punto fijo generados en la mutante en síncrona p.e. ['000000','000011']
                            ):
    
    '''Generar de forma Asíncrona la tabla de verdad de un edo inicial 
    con un gen siempre en un edo específico'''

    #lista con los edos en cada tiempo (resultado final a entregar)
    truth_tables = [edo_inicial]
        #NOTA: no cambio el valor de los nodos mutados porque 
        #en get_attrs_asyn_mut se usan unicamente edos iniciales que cumplan
        #con el valor de la mutante
        
    for t in range(1,pasos): #hacer análisis hasta los pasos maximos especificados
        #guardar diccionario con edo de cada nodo en el tiempo anterior (t)
        edos = {}
        for x in range(len(nodos)):
            edos[nodos[x]] = truth_tables[t-1][x]
    
        #escoger el nodo que se modificara
        #para reducir pasos elegir de los nodos que no estan mutados
        nodos_sin_gen_mut = [] #los lugares que tienen en la lista, no son nombres
        for a in range(len(nodos)):
            if nodos[a] not in gen_mut:
                nodos_sin_gen_mut.append(a)
        x = random.choice(nodos_sin_gen_mut) #nodo que se modificara para t+1
        
        #generar el nuevo valor en t+1 del nodo seleccionado
        r = reglas.split(';') #separar las reglas de cada nodo
        #cambiar ~,& y | por not,and y or
        regla = r[x].replace('~', 'not ').replace('&',' and ').replace('|',' or ')
        #cambiar nombres de nodos por sus edos en t
        for y in nodos:
            regla = regla.replace(y, edos[y])

        #guardar valor de cada nodo dependiendo el caso
        nuevo = ''
        for z in range(len(nodos)):
            #si es un nodo mutado dejar el edo_gen_mut:
            if nodos[z] in gen_mut:
                nuevo = nuevo + edo_gen_mut[gen_mut.index(nodos[z])]
            #si es el nodo x poner el valor generado arriba
            elif z == x:
                nuevo = nuevo + str(eval(regla)).replace('True','1').replace('False','0')
            #si no es el nodo x ni está mutado poner el valor en tiempo t
            else:
                nuevo = nuevo + edos[nodos[z]]

        #guardar el edo recien generado
        truth_tables.append(nuevo)
        
        #Parar si se llega a un edo correspondiente a un attr de punto fijo en Síncrona
        if nuevo == truth_tables[t-1]: #si ya se repitio una vez (esto facilita la función get_attrs)
            if nuevo in attrs_sincrona:
                return truth_tables
        
    return truth_tables
    
###############################

def get_attrs_asyn_mut(nodos, #lista de nombres de nodos p.e.: ['YAB','ARF3','miR','KAN','REV','AS12']
                        pasos, #integer con max de pasos en el tiempo que se correra la tabla p.e.: 500
                        reglas, #string con reglas logicas de cada nodo separadas con ';' p.e.: '~AS12;~AS12;(~REV&AS12);YAB;~miR;(REV&~KAN)'
                        gen_mut, #lista de nodos mutados p.e.: ['YAB','miR']
                        edo_gen_mut, #lista de edos nodos mutados p.e.: ['0','0']
                        attrs_sincrona, #lista con attrs punto fijo generados en la mutante en síncrona p.e. ['000000','000011']
                        repeticiones #integer con tiempos minimos en que se debe repetir un edo para ser attr de punto fijo p.e.: 100
             ):
    '''Generar de forma asíncrona los attrs de todos los edos iniciales para una mutante
    regresa 2 variables: attrs y tablas de verdad'''
    
    #crear todos los edos iniciales
    tabla = list(itertools.product([1,0], repeat = len(nodos)))
    #guardar los que cumplan con los valores de nodos mutados
    quitar = []
    for a in tabla:
        for b in range(len(gen_mut)):
            if a[nodos.index(gen_mut[b])] != int(edo_gen_mut[b]):
                quitar.append(a)
    lista = [k for k in tabla if k not in quitar]
    #convertirlos a string
    edos_iniciales = []
    for a in range(len(lista)):
        e = ''
        for b in lista[a]:
            e = e + str(b)
        edos_iniciales.append(e)
            
    #correr todos los edos iniciales usando get_truth_tabla_asyn_mut
    truth_tables = []
    for x in edos_iniciales:
        truth_tables.append(get_truth_table_asyn_mut(nodos, x, pasos, reglas, gen_mut, edo_gen_mut, attrs_sincrona))
    
    #Reunir los atractores
        #nota: esta función sólo guardará los attrs PF, los cíclicos no se buscarán
        #    sólo se nombrarán como tal
    attrs = []
    for a in truth_tables:
        #si no se llegó al max de pasos, entonces es un attr PF obtenido en sincronia
        if len(a) < pasos:
            attrs.append(a[-1])
        #si se llegó al max de pasos
        else:
            #guardar los últimos edos (según el número de repeticiones pedido)
            rep = []
            n = -1
            for b in range(repeticiones):
                rep.append(a[n])
                n-=1
            #si todos los últimos edos son iguales, es un attr PF
              #(si se repite el numero de veces especificado)
            if all(k == rep[0] for k in rep):
                attrs.append(rep[0])
            #si no, tomarlo como ciclico o trap-space
            else:
                attrs.append('ciclico o trap-space 3') #pongo el 3 porque lo he usado como referencia de attrs ciclicos en el pasado


    #reunir los attrs iguales en un mismo grupo para contarlos, sacar su vasija de atracción y resumir resultados
    attrs_final = {}
    for a in attrs:
        if a not in attrs_final:
            attrs_final[a] = 1
        else:
            attrs_final[a] += 1
    
    return attrs_final, truth_tables
