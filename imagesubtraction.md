# editted 

# Comet-hunting
import os
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
import scipy
from scipy.misc import imread, imsave
import glob

     
path='C:\\Users\\Fleko\\Documents\\Comet Hunting\\LASCO\\'

images = []

for root, dirs, files in os.walk(path):
    for file in files:
                images.append(file)
    		

i=0
x=scipy.misc.imread(path+images[i],flatten=1).astype(np.float32)
im=0
while i<len(images)-1:
   i=i+1 
   y=scipy.misc.imread(path+images[i],flatten=1).astype(np.float32)
   im=im+np.subtract(y,x)
   x=y
imsave(path+'subtraction.jpg',im)
