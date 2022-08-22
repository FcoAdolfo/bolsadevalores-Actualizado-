# bolsadevalores-Actualizado-



 Rosa Elena Peña (21-0415) 
 Francisco Adolfo (20-0866)
 
 ________________________________________________________________________________________________________________
 
 
 # Importar modulos

import mysql.connector as msql
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight')


 ________________________________________________________________________________________________________________
 
empdata = pd.read_csv('GOOGL.csv', index_col=False, delimiter = ',')
empdata.head()


________________________________________________________________________________________________________________



from mysql.connector import Error
try:
    conn = msql.connect(host='localhost', user='root',  
                        password='root') # Usuario y contraseña
    if conn.is_connected():
        cursor = conn.cursor()
        cursor.execute("CREATE DATABASE db_bolsadevalores")
        print("Database is created")
except Error as e:
    print("No se puede crear una base de datos con el mismo nombre en MySQL", e)
    
    
________________________________________________________________________________________________________________



from mysql.connector import Error
try:
    conn = msql.connect(host='localhost', database='db_bolsadevalores', user='root', password='root')
    if conn.is_connected():
        cursor = conn.cursor()
        cursor.execute("select database();")
        record = cursor.fetchone()
        print("You're connected to database: ", record)
        cursor.execute('DROP TABLE IF EXISTS yahoo_finance;')
        print('Creating table....')
# En la siguiente linea se creara la tabla "yahoo_finance" con sus culumnas y tipo de datos.
        cursor.execute("CREATE TABLE yahoo_finance(Fecha date,Open varchar(255),High varchar(255),Low varchar(255),Close varchar(255),AdjClose varchar(255),Volumen varchar(255))")
        print("Table is created....")
        #loop through the data frame
        for i,row in empdata.iterrows():
            # Aqui se le hace un insert into (%s significa que se le agregaran los datos del archivo .csv a la tabla que se creo anteriormente en mysql) 
            sql = "INSERT INTO db_bolsadevalores.yahoo_finance VALUES (%s,%s,%s,%s,%s,%s,%s)"
            cursor.execute(sql, tuple(row))
            print("Registros insertados")
            # commit () es uno de los varios métodos en Python que se utiliza para realizar las transacciones de la base de datos.
            conn.commit()
except Error as e:
            print("Error al conectarse a MySQL", e)
            
            
            
________________________________________________________________________________________________________________


# Graficar los datos

plt.figure(figsize = (10, 5))
plt.plot(empdata['Close'], label = 'Google Stock')
plt.title('Acciones de Yahoo Finance - Precio de las Acciones desde el 2016 al 2022')
plt.xlabel('14 de Enero 2016 al 14 de Agosto 2022')
plt.ylabel('Precio de cierre ($)')
plt.legend('Google')
plt.show()



________________________________________________________________________________________________________________



MVS30 = pd.DataFrame()
MVS30['Close'] = empdata['Close'].rolling(window = 30).mean()
MVS30


________________________________________________________________________________________________________________



MVS30[MVS30.index == 29]


________________________________________________________________________________________________________________



MVS100 = pd.DataFrame()
MVS100['Close'] = empdata['Close'].rolling(window = 100).mean()
MVS100


________________________________________________________________________________________________________________



MVS100[MVS100.index == 99]


________________________________________________________________________________________________________________



# Graficar los datos

plt.figure(figsize = (10, 5))
plt.plot(empdata['Close'], label = 'Google Stock')
plt.plot(MVS30['Close'], label = 'Media Movil 30')
plt.plot(MVS100['Close'], label = 'Media Movil 100')
plt.title('Acciones de Yahoo Finance - Precio de las Acciones desde el 2016 al 2022')
plt.xlabel('14 de Enero 2016 al 14 de Agosto 2022')
plt.ylabel('Precio de cierre ($)')
plt.legend(loc = 'upper left')
plt.show()


________________________________________________________________________________________________________________



data = pd.DataFrame()
data['Google'] = empdata['Close']
data['MVS30'] = MVS30['Close']
data['MVS100'] = MVS100['Close']
data



________________________________________________________________________________________________________________




def senal(data):
    compra = []
    venta = []
    condicion = 0
    
    for dia in range(len(data)):
        
        if data['MVS30'][dia] > data['MVS100'][dia]:
            if condicion != 1:
                compra.append(data['Google'][dia])
                venta.append(np.nan)
                condicion = 1
            else:
                compra.append(np.nan)
                venta.append(np.nan)
            
        elif data['MVS30'][dia] < data['MVS100'][dia]:
            if condicion != -1:
                venta.append(data['Google'][dia])
                compra.append(np.nan)
                condicion = -1
            else:
                compra.append(np.nan)
                venta.append(np.nan)
        else:
            compra.append(np.nan)
            venta.append(np.nan)
            
    return (compra, venta)
    
    
    
________________________________________________________________________________________________________________
    
    
    
    
   senales = senal(data)
data['Compra'] = senales[0]
data['Venta'] = senales[1]
data


________________________________________________________________________________________________________________



#Grafica Yahoo Finance

plt.figure(figsize = (10, 5))
plt.plot(data['Google'], label = 'Google', alpha = 0.3)
plt.plot(data['MVS30'], label = 'MVS30', alpha = 0.3)
plt.plot(data['MVS100'], label = 'MVS100', alpha = 0.3)
plt.scatter(data.index, data['Compra'], label = 'Precio de Compra', marker = '^', color = 'green')
plt.scatter(data.index, data['Venta'], label = 'Precio de Venta', marker = 'v', color = 'red')
plt.title('Acciones de Yahoo Finance - Precio de sus acciones desde el 2016 - 2022')
plt.xlabel('14 Enero 2016 - 14 Agosto. 2022')
plt.ylabel('Precio de cierre ($)')
plt.legend(loc = 'upper left')
plt.show()



_______Fin______




use db_bolsadevalores;
show tables;
select * from yahoo_finance  limit 50;  

(En MySQL con este codigo usando la base de datos hago un select * from a la tabla con un limite de 50 registros o la cantidad que desee)


Archivo .csv
[GOOGL.csv](https://github.com/FcoAdolfo/bolsadevalores-Actualizado-/files/9395571/GOOGL.csv)

