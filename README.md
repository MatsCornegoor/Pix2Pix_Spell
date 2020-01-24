 The first step is to clone the pix2pix repo onto your local workspace and cd into the repository.
$ git clone https://github.com/affinelayer/pix2pix-tensorflow.git
$ cd pix2pix-tensorflow


# First we need a dataset. We can download it directly onto Spell and mount it to our future runs. Make a note of the run id for the run downloading your dataset.
#  
$ spell run "python tools/download-dataset.py facades"


# Alternatively we can upload our custom made dataset. To upload a dataset, use the spell upload command. First create a new folder in the root folder and make sure your dataset is inside it.

# Folder structure:

# Root
#     DATASET_FOLDER
#         train
#         val


# Let spell know the dataset folder

git add .
git commit -m "added new dataset"

# upload the dataset

$ spell upload DATASET_FOLDER


# We can check to see if the folder has uploaded

$ spell ls uploads/DATASET_FOLDER


# to train the model, we need to tell the pix2pix Python script where our --input_dir is, select wich machine we want to use and set some other options. If you downloaded a spell dataset using a run, then we have to mount it. To do this, the first line has to change to: spell run -m runs/RUN_ID/:dataset \

$ spell run \
"python pix2pix.py \
--mode train \
--output_dir \
facades_train \
--max_epochs 200 \
--input_dir data4/train \
--which_direction AtoB \
--ngf 32 \
--ndf 32"


# Copy the result folder. This takes some time. You can also download the file from the web interface
spell cp runs/3/facades_train


# test the model
cd ..
python pix2pix.py \
  --mode test \
  --output_dir facades_test \
  --input_dir facades/val \
  --checkpoint facades_train


# The pix2pix script automatically creates an html file so you can view the results of your test run on a local site.

$ open facades_test/index.html 


# export the model
python pix2pix.py --mode export --output_dir export/ --checkpoint facades_train/ --which_direction AtoB
# It will create a new export folder

# Port the model to tensorflow.js (python 2 doesn’t have tempfile, so use python3 instead)
cd server
cd static
mkdir models
cd ..
python3 tools/export-checkpoint.py --checkpoint ../export --output_file static/models/facades_AtoB.pict


# For a interactive demo, Copy the model we got to the `models` folder and change the source file in index.js
