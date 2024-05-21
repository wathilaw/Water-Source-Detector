# Water-Source-Detector
import cv2
import numpy as np
import tkinter as tk
from tkinter import filedialog, messagebox

class Water_Source_Detector:
    def __init__(self,root):
        self.root = root
        self.root.title("Water Source Detector")
        
        self.lbutton = tk.Button (root, text="Load your map", command = self.lmap) #lmap = load map, lbutton = load button
        self.lbutton.pack(pady = 10)
        
        self.dbutton = tk.Button (root, text="Detect Water Sources ", command = self.dws) #dws = detect water source, dbutton = detect button
        self.dbutton.pack(pady = 5)
        
        self.clabel = tk.Label(root, text="Detected Water Sources: 0") #clabel = count label
        self.clabel.pack(pady = 10)
        
        self.m_image =None #m_image = map_image
        
    def lmap(self):
        open_map = filedialog.askopenfilename(filetypes =(("Image files", "*.jpg;*.jpeg;*.png"),("All files", "*.*")))
        if open_map:
            self.m_image= cv2.imread(open_map)
            if self.m_image is not None:
                cv2.imshow("Map Image", self.m_image)
                cv2.waitKey(0)
                cv2.destroyAllWindows()
            else:
                messagebox.showerror("Error", "Failed to load the image. Try loading the correct type")
                
    def dws(self):
        if self.m_image is None:
            messegebox.showerror("Error", "Please load an image of a map first")
            return
        hsv_image = cv2.cvtColor(self.m_image, cv2.COLOR_BGR2HSV)
        
        lower_blue = np.array([90,50,50])
        upper_blue = np.array([140,255,255])
        
        mask = cv2.inRange(hsv_image,lower_blue,upper_blue)
        
        contours,_ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
        
        min_threshold = 5
        water_contours = [contour for contour in contours if cv2.contourArea(contour) > min_threshold]
        
        map_image_copy = self.m_image.copy()
        cv2.drawContours(map_image_copy,water_contours, -1, (0,0,255),2)
        
        cv2.imshow("Detect Water Sources",map_image_copy)
        cv2.waitKey(0)
        cv2.destroyAllWindows()
        
        water_source_count = len(water_contours)
        self.clabel.config(text = f"Detect Water Source:{water_source_count}")
        
def main():
    root = tk.Tk()
    app = Water_Source_Detector(root)
    root.mainloop()
    
if __name__ =="__main__":
    main()
