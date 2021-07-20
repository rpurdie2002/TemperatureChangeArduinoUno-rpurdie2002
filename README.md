# TemperatureChangeArduinoUno-rpurdie2002
Measuring the freezing/melting point of water using an RTD and Arduino Uno circuit system. All coding done in python. 

#import all libraries required

import pyfirmata as pf
import matplotlib.pyplot as plt
import time
import numpy as np

port = '/dev/cu.usbserial-1430'
board = pf.Arduino(port)

# iterator is used to read all imports from the circuit

it = pf.util.Iterator(board)

#initiates the iterator to run loop against code, will retrive updated input values

it.start()

#seperate file created to record data
def dataFile(name, listName1, listName2):
    with open(name, 'w') as f:

        for x,y in zip(listName1, listName2):
            stringIn = "{} {}\n".format(x,y)
            f.write(stringIn)
            
#specify the pin from board

analog_0 = board.get_pin('a:0:i')

V = []

#retrieves 20 voltage values with a 5 second interval between each recording

for i in range(120):
    time.sleep(60)
    V.append(analog_0.read()* 5.0)
    
#make voltage into a numpy array to continue with calculations

V = np.array(V)
print(V)

#plot voltage values from the set time alotted

plt.plot(range(0,120), V, 'b-o')
plt.xlabel("Time (s)")
plt.ylabel("Voltage (V)")
plt.xlim(0,240)
plt.ylim(0,5)
plt.show()

#determine current running through system

Rk = 1000
I = (5-V)/Rk
print(I)

Ru = V/I
print(Ru)

#determine temperture values for each resistance recording

T0 = 0
alpha = 0.00392
T = (Ru/1000 - 1)/alpha + T0
print(T)

#plot voltage values from the set time alotted
plt.plot(range(0,10), T, 'b-o')
plt.xlabel("Time (s)")
plt.ylabel("Temperature (C)")
plt.xlim(0,240)
plt.ylim(-20,35)
plt.show()
