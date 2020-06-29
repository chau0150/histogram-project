# histogram-project
#this is my image processing project for hstogram equalization

import matplotlib.pyplot as plt
from PIL import Image
import numpy as np

# OPEN IMAGE
PATH = "" # ENTER IMAGE PATH HERE
image = Image.open(PATH)

# PROCESS IMAGE
img = np.array(image)
L = np.max(img) + 1

# FUNCTIONS 
# Plot Image Histogram
def calcHist(img): 
# Error Check 
if type(img) != np.ndarray:
print("Wrong Datatype. Input a numpy.ndarray")
return
# Image Dimensions 
rows = img.shape[0] 
cols = img.shape[1] 
# Intensity Freq Array
hist = np.zeros(L) 
# Intensity Freq Calc 
for i in range(rows): 
for j in range(cols):
r = img[i, j] 
hist[r] += 1
return hist
# Plot Image Cummulative Frequencies
def calcCF(img):
# Error Check
if type(img) != np.ndarray:
print("Wrong Datatype. Input a numpy.ndarray")
return 
# Image Dimensions
rows = img.shape[0] 
cols = img.shape[1]
MN = rows * cols
# Intensity Freq Array
hist = calcHist(img) 

# Probability Mass Function 
pmf = hist / MN 
# Cummulative Frequency Array 
cumFreq = [] 
# Cummulative Frequenct Calculation
for i in range(L): 
cf = np.sum(pmf[0 : i+1]) 
cumFreq.append(cf)

return np.array(cumFreq)
# Image Histogram Equalization
def eqHistImg(img): 
# Error Check 
if type(img) != np.ndarray: 
print("Wrong Datatype. Input a numpy.ndarray") 
return 
# Image Dimensions
rows = img.shape[0] 
cols = img.shape[1] 
MN = rows * cols
# Cummulative Frequency Array
cumFreq = calcCF(img) 
# Scale Cummulative Frequencies 
scaledFreq = cumFreq * (L - 1)
# Initialize Equalized
eqImg = img.copy() 

for i in range(rows): 
for j in range(cols):
r = img[i, j] 
sf = scaledFreq[r]
rNew = int(np.round(sf)) 
eqImg[i, j] = rNew 
return eqImg

# PERFORM IMAGE EQUALIZATION
eqImg = eqHistImg(img) 
# SHOW RESULTS AND COMPARE 
orgHist = calcHist(img)
orgCF = calcCF(img)
eqHist = calcHist(eqImg)
eqCF = calcCF(eqImg)
plt.figure(figsize = (15, 20)) 
plt.subplot(3, 2, 1)
plt.title("Original Image", fontsize = 13)
plt.xticks([])
plt.yticks([]) 
plt.imshow(img, "gray")

plt.subplot(3, 2, 2)
plt.title("Equalized Image", fontsize = 13)
plt.xticks([]) 
plt.yticks([])
plt.imshow(eqImg, "gray")
plt.subplot(3, 2, 3)
plt.title("Histogram - Original Image", fontsize = 13)
plt.xlabel("Intensity Value", fontsize = 10)
plt.ylabel("Frequency", fontsize = 10)
plt.plot(np.linspace(0, L - 1, L), orgHist) 

plt.subplot(3, 2, 4)
plt.title("Histogram - Equalized Image", fontsize = 13)
plt.xlabel("Intensity Value", fontsize = 10)
plt.ylabel("Frequency", fontsize = 10) 
plt.plot(np.linspace(0, L - 1, L), eqHist)

plt.subplot(3, 2, 5)
plt.title("Cummulative Frequencies - Original Image", fontsize = 13)
plt.xlabel("Intensity Value", fontsize = 10)
plt.ylabel("Cummulative Frequency", fontsize = 10)
plt.plot(np.linspace(0, L - 1, L), orgCF)
plt.plot(np.linspace(0, L - 1, L), np.linspace(0, 1, L), c = 'orange', ls = "--") 

plt.subplot(3, 2, 6)
plt.title("Cummulative Frequencies - Equalized Image", fontsize = 13)
plt.xlabel("Intensity Value", fontsize = 10)
plt.ylabel("Cummulative Frequency", fontsize = 10) 
plt.plot(np.linspace(0, L - 1, L), eqCF)
plt.plot(np.linspace(0, L - 1, L), np.linspace(0, 1, L), c = 'orange', ls = "--")
plt.savefig("Results") 
plt.show()
