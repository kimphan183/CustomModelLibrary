
import random
import re
import os
from maya import cmds
from maya import mel
from maya import OpenMayaUI as omui
from PySide2.QtCore import *
from PySide2.QtGui import *
from PySide2.QtWidgets import *
from shiboken2 import wrapInstance

class CustomLibraryWindow(QWidget):

	def __init__(self, parent=None):

    	super(CustomLibraryWindow, self).__init__(parent)
    	self.setWindowFlags(Qt.Window)
    	self.setWindowTitle('Custom Model Library')

    	self.setMinimumWidth(770)
    	self.setMinimumHeight(400)
    	hLayout = QHBoxLayout() #horizontal
    	self.setLayout(hLayout)

    	# Add widget for "Create Model From Selection" button
    	self.create_model_button = QPushButton(text="Create Model \n From Selection", parent=self)
    	hLayout.addWidget(self.create_model_button)
   	 
    	# Add widget for model list
    	self.model_list = QListWidget()
    	self.model_list.setResizeMode(QListView.Adjust)
    	hLayout.addWidget(self.model_list)
   	 
    	# Add widget for "Import Model To Scene" button
    	self.import_model_button = QPushButton(text="Import Model \n To Scene", parent=self)
    	hLayout.addWidget(self.import_model_button)

    	# Add widget for "Remove Model From Library" button
    	self.remove_model_button = QPushButton(text="Remove Model\nFrom Library", parent=self)
    	hLayout.addWidget(self.remove_model_button)

    	# Connect buttons with the actions
    	self.create_model_button.clicked.connect(self.create_model_from_selection)
    	self.import_model_button.clicked.connect(self.import_model_to_scene)
    	self.remove_model_button.clicked.connect(self.remove_model_from_library)

    	self.load_custom_model() #load existing custom models

	def load_custom_model(self):

    	# Clear the existing items in the model list widget
    	self.model_list.clear()

    	# Get the custom library directory path
    	library_dir = "/net/homes/{0}/custom_library".format(os.environ['USER'])
    	if not os.path.exists(library_dir):
   		 return

    	# Get the list of model files in the custom library directory
    	model_files = [f for f in os.listdir(library_dir) if f.endswith('.fbx')]

    	self.model_list.setViewMode(QListView.IconMode)
    	self.model_list.setIconSize(QSize(150,150))

    	# Add information to the model list widget
    	for model_file in model_files:
        	# Add name
        	model_name = os.path.splitext(model_file)[0]

        	# Add file size
        	file_path = os.path.join(library_dir, model_file)
        	file_size = os.path.getsize(file_path)
        	file_size_str = self.format_file_size(file_size)

        	# Load the model file
        	cmds.file(file_path, i=True)
		# Add polycount
        	polycount = cmds.polyEvaluate(f=True)


        	item = QListWidgetItem("Name: " + model_name + "\nFile Size: " + file_size_str + polycount) # Display info on widget
        	self.model_list.addItem(item)

        	images_dir = "/net/homes/{0}/custom_library/images".format(os.environ['USER'])
        	image_path = os.path.join(images_dir, model_name + '.0000.png')   
        	icon = QIcon(image_path)
        	item.setIcon(icon)
	def create_model_from_selection(self):

    	# Get the selected model in the scene
    	selected_model = cmds.ls(selection=True)
    	if not selected_model:
   		 print("please select one")
    	model_name = selected_model[0]
    	print("Got the model: " + model_name)

    	# Create the custom library on local computer if it hasn't existed
    	library_dir = "/net/homes/{0}/custom_library".format(os.environ['USER'])
    	if not os.path.exists(library_dir):
   		 os.makedirs(library_dir)
    	print library_dir

    	# Define the file path for the model
    	file_path = os.path.join(library_dir, model_name + '.fbx')

    	# Export the selected model as an FBX file
    	cmds.file(file_path, force=True, exportSelected=True, type='FBX export')

    	print("Successfully created the model: " + model_name)

    	# Refresh the model list widget
    	self.load_custom_model()
   	 
    	# Capture image from viewport
    	images_dir = "/net/homes/{0}/custom_library/images".format(os.environ['USER'])
    	image_path = captured_image_from_viewport(images_dir, model_name)

    	# Refresh the model list widget after a delay of 1 second
    	QTimer.singleShot(1000, self.load_custom_model)
	
		def import_model_to_scene(self):

    	# Get the selected item in the model list widget
    	selected_item = self.model_list.currentItem()
    	if not selected_item:
   		 print("Please select a model to import")
   		 return

    	# Get the model name from the selected item
    	model_info = selected_item.text().split("\n")
    	model_name = model_info[0].split(": ")[1]

    	# Get the custom library directory path
    	library_dir = "/net/homes/{0}/custom_library".format(os.environ['USER'])
    	if not os.path.exists(library_dir):
   		 print("Custom library directory doesn't exist")
   		 return

    	# Get the file path for selected model
    	file_path = os.path.join(library_dir, model_name + '.fbx')

    	# Check if the file exists
    	if not os.path.isfile(file_path):
   		 print("Selected model file doesn't exist")
   		 return

    	# Import the model into scene
    	cmds.file(file_path, i=True)
    	print("Sucessfully imported the model: " + model_name)

	def remove_model_from_library(self):

    	# Get the selected item in the model list widget
    	selected_item = self.model_list.currentItem()
    	if not selected_item:
        	print("Please select a model to remove")
        	return

    	# Get the model name from the selected item
    	model_info = selected_item.text().split("\n")
    	model_name = model_info[0].split(": ")[1]

    	# Get the custom library directory path
    	library_dir = "/net/homes/{0}/custom_library".format(os.environ['USER'])
    	if not os.path.exists(library_dir):
        	print("Custom library directory doesn't exist")
        	return

    	# Get the file path for the selected model
    	file_path = os.path.join(library_dir, model_name + '.fbx')
 
    	# Check if the file exists
    	if not os.path.isfile(file_path):
        	print("Selected model file doesn't exist")
        	return

    	# Remove the file from the library directory
    	os.remove(file_path)
    	print("Successfully removed the model from the library: " + model_name)

    	# Get the directory path where the playblast images are stored
    	images_dir = "/net/homes/{0}/custom_library/images".format(os.environ['USER'])

    	# Define the prefix for the playblast images based on the model name
    	image_prefix = model_name + '.'
     	# Get the list of playblast image files that match the prefix
    	image_files = [f for f in os.listdir(images_dir) if f.startswith(image_prefix)]
   	 
    	for image_file in image_files:
        	image_path = os.path.join(images_dir, image_file)
        	os.remove(image_path)
        	print("Successfully removed the playblast image: " + image_file)

    	# Refresh the model list widget
    	self.load_custom_model()

	def format_file_size(self, size):
    	# Format the file size into a human-readable format
    	power = 2 ** 10
    	n = 0
    	units = ["B", "KB", "MB", "GB", "TB"]
    	while size > power:
        	size /= power
        	n += 1
    	return "{:.2f} {}".format(size, units[n]) # Format: Round to 2 decimals

def captured_image_from_viewport(directory, model_name):
	# Capture image of the model from the viewport
	image_path = os.path.join(directory, model_name)
   	 
	# Set the viewport settings for playblast
	width, height = cmds.getAttr("defaultResolution.width"), cmds.getAttr("defaultResolution.height")

	camera_name = 'persp'  # Viewport camera name
	cmds.viewFit(camera_name, allObjects=True, animate=False) # Set the viewport camera to the correct framing

	cmds.lookThru(cmds.modelEditor('modelPanel4', query=True, camera=True))  # Set the active camera for playblast
	cmds.modelEditor('modelPanel4', edit=True, displayAppearance='smoothShaded')  # Set the display appearance
	cmds.modelEditor('modelPanel4', edit=True, allObjects=True)  # Show all objects in the viewport
    
	# Perform the playblast to capture the image
	cmds.playblast(format='image', frame=1, filename=image_path, sequenceTime=False, # Capture only 1 frame
               	clearCache=True, viewer=False, showOrnaments=False,
               	offScreen=True, width=width, height=height)

	return image_path


def get_main_window():
	ptr = omui.MQtUtil.mainWindow()
	maya_main_window = wrapInstance(int(ptr), QWidget)
	return maya_main_window

def show_custom_model_library():
	maya_window = get_main_window()
	custom_model_library = CustomLibraryWindow(maya_window)
	custom_model_library.show()

show_custom_model_library()

