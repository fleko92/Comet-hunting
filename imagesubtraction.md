# edit 

# Comet-hunting
import os
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
import scipy
from scipy.misc import imread, imsave
import glob

import re, sys, urllib.request, datetime
from robobrowser import RoboBrowser
from PIL import Image

# gets the size of the crop required from the date.
# Based on where sungrazers will be at the time of year.
# locations taken from sungrazer.nrl.navy.mil/index.php?p=comet_tracks
def getCropBox( date, res ):
    cropsizes = [(0, res/2, res/2, res),            #jan
                 (0, res/2, res/2, res),            #feb
                 (0, res/2, res/2, res),            #mar
                 (res/4, res/2, 3*res/4, res),      #apr
                 (res/2, res/2, res, res),          #may
                 (res/2, res/2, res, res),          #jun
                 (res/2, res/2, res, res),          #jul
                 (res/2, res/2, res, res),          #aug
                 (res/2, res/2, res, res),          #sep
                 (res/2, res/2, res, res),          #oct
                 (res/4, res, 3*res/4, res),        #nov
                 (res/4, res/2, 3*res/4, res) ]     #dec

    if date == '':                   
        month = datetime.datetime.now().strftime("%m")
    else:
        month = int(date[4:6])
    return cropsizes[int(month) -1 ]
    
#constants for the data type
resolution = '512' # the cropping assumes 1024
imageType = 'LASCO C3'
date = '20070205' # YYYMMDD


# open the page with the web form using robobrowser.
url = "http://sohodata.nascom.nasa.gov/cgi-bin/data_query/html/"
print("Trying to open ", url, "...")
try: 
    browser = RoboBrowser()
    browser.open(url)
except:
    print("Link not reached, check connection")
    sys.exit()
    
print("Link oppened successfully. Ignore warning")

# get the form required (the google search is also a form).fill in the data & submit.
# here i'm using the C3 sensor, and getting them in 1024. The others are left blank to just get todays. Will want to run at 11:50ish
form = browser.get_form(action=re.compile(r'sohodata'))
form['Types'] = imageType
form['Resolution'] = resolution
if date != '':
    form['Start'] = date
browser.submit_form(form)

# to count the downloaded images, and remember their filenames
imgNo = 0
filenames = []


# iterate through all of the <a> tags, check they're a link and then download the data.
for link in browser.find_all('a'):
    if "http" not in link.get('href'):
        continue
    else:
        # download from the url and the filename in the <a> tag
        filenames.append(link.string)
        urllib.request.urlretrieve(link.get('href'), filenames[imgNo])
        imgNo += 1
        print("Downloaded image ", imgNo)

print("Successfully downloaded ", imgNo, " photos.")

for name in filenames:
    im = Image.open(name)
    im.crop(getCropBox(date,int(resolution))).save(name)


     
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
