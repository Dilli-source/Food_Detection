# Food_Object_detection
Identifying the Food Items using Tensorflow and Opencv


# A brief overview of the steps needed to do Object Detection:

 1.	Collect a few hundred images that contain your object - The bare minimum would be about 100, ideally more like 500+, but, the more         images you have, the more tedious step 2 is.
 2.	Annotate/label the images, ideally with a program. I personally used LabelImg. This process is basically drawing boxes around your         object in an image. The label program automatically will create an XML file that describes the object in the pictures.
 3.	Split this data into train/test samples
 4.	Generate TF Records from these splits
 5.	Setup a .config file for the model of choice 
 6.	Train
 7.	Export graph from new trained model
 8.	Detect custom objects in real time!
 
 # We Will Look into Each Steps in Detail.
 
    Step 1:
     •	Collect Images that are required to be identified.
     
    Step 2: Labelling the image
     •	For this, I am going to use LabelImg, you can grab it with git clone https://github.com/Dilli-source/labelImg.git, or just          download and extract the zip.
     
   Installation instructions are on the [labelimg github]( https://github.com/Dilli-source/labelImg.git), but for Python3 on Ubuntu:
   
     •	sudo apt-get install pyqt5-dev-tools
     •	sudo pip3 install lxml
     •	make qt5py3
   note: These are the dependencies needed to run labelimg python file
   
   Traverse to the folder where the labelimg.py is downloaded(path to the Labellimg.py)and run the following command
     
      python3 labelImg.py
      
   The program will open a window similar to the below mention figure:
   ![alt text](https://github.com/Dilli-source/labelImg/blob/master/demo/window.JPG)
   
   From here, choose to open dir and pick the directory that you saved all of your images to. Now, you can begin to annotate with the create rectbox button. Draw your box, add the name in, and hit ok. Save, hit next image, and repeat! You can press the w key to draw the box and do ctrl+s to save faster. Not sure if there's a shortcut for the next image.
   
   note: Instructions for creating Bounding Box is available in [labelimg github]( https://github.com/Dilli-source/labelImg.git)
   
   Once you have over 100 images labeled, we're going to separate them into training and testing groups. To do this, just copy about 10% of your images and their annotation XML files to a new dir called test and then copy the remaining ones to a new dir called train
   
   Once you've done all of this, you're ready with the data, where we're going to cover how we can create the required TFRecord files from this data to train an object detection model.
   
   At this point, you should have an images directory, inside of that has all of your images, along with 2 more diretories: train and test. Inside the test directory should be a copy of ~10% of your images with their XML annotation data, and then the training directory should have a copy of the rest.
   
   Now we need to convert these XML files to singular CSV files that can be then converted to the TFRecord files. To do this, I am going to make use of some of the code from datitran's github, with some minor changes. To begin, we're going to use xml_to_csv.py.
   
   Open the file and change the Directory structure if you are using or running the process in Different directory.
   
    And then run the following command
    
    python3 xml_to_csv.py
    
   This just handles for the train/test split and naming the files something useful. Go ahead and make a data directory, and run this to create the two files. Next, create a training directory from within the main Object-Detection dir. At this point, you should have the following structure, 
   
    Object-Detection
      -data/
      --test_labels.csv
      --train_labels.csv
    -images/
     --test/
      ---testingimages.jpg
     --train/
      ---testingimages.jpg
     --...yourimages.jpg
    -training
    -xml_to_csv.py
   
  Now, grab generate_tfrecord.py. The only modification that you will need to make here is in the class_text_to_int function. You need to change this to your specific class. In our case, we just have 18 class. If you had many classes, then you would need to keep building out this if statement.
  
  A small snippet from my code:

    # TO-DO replace this with label map
     def class_text_to_int(row_label):
       if row_label == 'egg':
         return 1
       if row_label == 'venpongal':
         return 2
       if row_label == 'vada':
         return 3
       if row_label == 'parota':
         return 4
       if row_label == 'poha':
         return 5
       if row_label == 'briyani':
         return 6
       else:
        None
  Next, in order to use this, we need to either be running from within the models directory of the cloned models github, or we can more formally install the object detection API.
  
  I am doing this steps on a fresh machine to be certain I don't miss any steps, so I will be fully setting up the Object API. If you've already cloned and setup, feel free to skip the initial steps and pick back up on the setup.py
  
  First, I am cloning the repository to my specific Directory:
  
  note: if you dont have git installed in your windows then you can [git download]( https://git-scm.com/download/win)

  git clone https://github.com/tensorflow/models.git
  
  Then, following the installation instructions:
   
    # Install TensorFlow for cpu
      pip install tensorflow 
      
    # Install TensorFlow for gpu
      pip install tensorflow-gpu
    
    # Install all other dependencies
      
      pip install pillow
      pip install lxml
      pip install jupyter
      pip install matplotlib.
      
   # Download Google Protobuf
   The TensorFlow Object Detection API uses .proto files which need to be compiled into .py files. Google provides a program called Protobuf that will batch compile these for you.

   Note: Download version 3.4.0 for Windows, as later versions may give you a “Permission Error” when trying to compile. Use the link, then download [protoc-3.4.0-win32.zip](https://github.com/protocolbuffers/protobuf/releases/tag/v3.4.0).
     
   # Extract the Protobuf download.
   You can extract to C:/Program Files, or, like I did, you can extract to C:/Users/your-username.
   
   Execute the protobuf compile within the command prompt.
   
   Open command prompt, move to the location of your tensorflow/models/research directory (wherever you put it in).
   
   cd D:\object-detection\models\research
   
   Once you’re in the models\research directory, execute the protobuf compile using the location of the “protoc.exe” file. This is the location in which you extracted the Protobuf download.
   
    “C:\Program Files\bin\protoc.exe” object_detection/protos/*.proto --python_out=.

   Note: you can test if this has worked by going to the object_detection/protos folder, and if there are .py files, you’ve successfully completed the compilation of your .proto files!
   
   Add libraries to PYTHONPATH
   
   The models/research directories will need to be added to your PYTHONPATH so that the system recognizes the files within these folders. This can be done on Git Bash, or by manually setting your paths.
   
   To manually set the paths, go to Go to System -> Advanced system settings -> Environment Variables -> New, and add a variable with the name PYTHONPATH and these values:
   
   ![alt text](https://github.com/Dilli-source/Food_Object_detection/blob/master/images/environmental.JPG)
   
   Note: You may have to restart your computer/system for this to take effect.
   
   Finally, let's install the object_dection library formally by doing the following from within the models directory:

    python3 setup.py install
   
   If you face any issues while running this command, then probably you system is not installed with visual studio . Install it and if you find it difficult you can refer the [stack overflow](https://stackoverflow.com/questions/33323172/vcvarsall-bat-needed-for-python-to-compile-missing-from-visual-studio-2015-v-1) page fixing the error.
   
   Now we can run the generate_tfrecord.py script. We will run it twice, once for the train TFRecord and once for the test TFRecord.
     
    python3 generate_tfrecord.py --csv_input=data/train_labels.csv --output_path=data/train.record --image_dir=images/
    python3 generate_tfrecord.py --csv_input=data/test_labels.csv --output_path=data/test.record --image_dir=images/
    
   Now, in your data directory, you should have train.record and test.record.
   Next up, we need to setup a configuration file and then either train a new model.
   
   we will train our object detection model to detect our custom object. To do this, we need the Images, matching TFRecords for the training and testing data, and then we need to setup the configuration of the model, then we can train. For us, that means we need to setup a configuration file.

Here, we have two options. We can use a pre-trained model, and then use transfer learning to learn a new object, or we could learn new objects entirely from scratch. The benefit of transfer learning is that training can be much quicker, and the required data that you might need is much less. For this reason, we're going to be doing transfer learning here.

TensorFlow has quite a few pre-trained models with checkpoint files available, along with configuration files. You can do all of this yourself if you like by checking out their configuring jobs documentation. The object API also provides some sample configurations to choose from.

I am going to go with mobilenet, using the following checkpoint and configuration file

    wget https://raw.githubusercontent.com/tensorflow/models/master/object_detection/samples/configs/ssd_mobilenet_v1_pets.config
    wget http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v1_coco_11_06_2017.tar.gz
    
 You can check out some of the other checkpoint options to start with [here](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md).
 
 Put the config in the training directory, and extract the ssd_mobilenet_v1 in the models/object_detection directory
 
 •	Inside training dir, add object-detection.pbtxt:
       
    item {
     id: 1
     name: 'name of object to be Identified'
     }
 •	Based on number of object keep adding the id in object detection.pbtxt


# Training:
•	within models/object_detection: 
•	Run the Following Command

    python3 train.py --logtostderr --train_dir=training/ --pipeline_config_path=training/ssd_mobilenet_v1_pets.config
    
•	 you should see output like this:

    INFO:tensorflow:global step 11788: loss = 0.6717 (0.398 sec/step)
    INFO:tensorflow:global step 11789: loss = 0.5310 (0.436 sec/step)
    INFO:tensorflow:global step 11790: loss = 0.6614 (0.405 sec/step)
    INFO:tensorflow:global step 11791: loss = 0.7758 (0.460 sec/step)
    INFO:tensorflow:global step 11792: loss = 0.7164 (0.378 sec/step)
    INFO:tensorflow:global step 11793: loss = 0.8096 (0.393 sec/step)
    
•	Wait until the training is done.

•	Your steps start at 1 and the loss will be much higher. Depending on your GPU and how much training data you have, this process will take varying amounts of time. 

•	 You can check how the model is doing via TensorBoard. Your models/object_detection/training directory will have new event files that can be viewed via TensorBoard.

•	From models/object_detection, via terminal, you start TensorBoard with:

    tensorboard --logdir='training'
    
•	This runs on 127.0.0.1:6006 (open in your browser)

•	You may see there should be loss in graph if not try increasing the training samples.

# Export graph from new trained model:

•	To run this, you just need to pass in your checkpoint and your pipeline config, then wherever you want the inference graph to be placed. For example:

    python3 export_inference_graph.py \
       --input_type image_tensor \
       --pipeline_config_path training/ssd_mobilenet_v1_pets.config \
       --trained_checkpoint_prefix training/model.ckpt-100000 \
       --output_directory food_inference_graph
       
•	Take the final steps only if it has all three files in the model after training

•	If you get an error about no module named 'nets', then you need to re run:
      
      # From tensorflow/models/
        export PYTHONPATH=$PYTHONPATH:`pwd`:`pwd`/slim
      # switch back to object_detection after this and re run the above command

•	This will create a .pb file (i.e a frozen model)

    
   
    
     
   

     
    
     
   
