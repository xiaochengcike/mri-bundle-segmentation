# Segmentation of Neuron Bundles from Diffusion MRI

## Project Description
Project for Statistical Learning Theory Course at ETH Zurich by Antonio Orvieto, Moisés Torres García and Sjoerd van Bekhoven.

## Repository Structure
* code/
  * data/ 
* literature/
  * papers/
* report/
* results/

## Pre-processing
You can either do the pre-processing yourself, or download the following two files and place them in "code/data":
* embeddings (download at https://drive.google.com/file/d/0B-BbrQ1IXzeqa2drbkQyaTBjZ0E)
* FA (download at https://drive.google.com/open?id=0B-BbrQ1IXzeqSmpZek1jT1RWZVU)

If you want to do the pre-processing yourself, follow these steps:

1. Make sure the following files are present in the folder "code/data/":
  * embeddings
  * diff_data.nii.gz
2. Add the "NIfTI" MATLAB library to your path (download at http://ch.mathworks.com/matlabcentral/fileexchange/8797-tools-for-nifti-and-analyze-image)
3. In "code/preprocessing/main.m", check if all paths are correctly for your system (they should be right away) and run all sections subsequently.
4. Make sure the files "embeddings" and "FA" generated by the script are in the folder "code/data/".

## Instructions
* It is necessary to execute the files with Python 3.
* Make sure you have created the folder "code/results/" to save the results of the experiments.

* Make sure that in the folder "code/data/" the following files are present (see steps under pre-processing):
  * embeddings
  * FA

### Create a subset
First, we create a subset of the data to run the algorithm on. Do this by opening "code/subset.py" and change the global variables x\_sub, y\_sub and z\_sub to the subset you want. Then, run the file. This will create three files in the folder "code/data/": "dim\_sub.npy" , "embedding\_sub.npy" and "FA\_sub.npy", which will be used by the other scripts. If you want to see what the subset looks like, run "code/plot_subset.py".

### Spotting the superparamagnetic phase
1. We are ready to use the Svendsen-Wang Monte Carlo (SWMC) algorithm, located in "code/swmc.py". At the top of the file there are a couple of variables which you can change. The most important one is the "type" variable, which should now be set to "swmc". 
2. After that, set the following variables:
   * q = num. of pot spin variables
   * mc\_iterations = num. of iterations per MC
   * mc\_burn\_in = number of burn-in samples for MC (must be < mc\_iterations!)
   * k\_neighbors = number of nearest neighbors
   * wm\_threshold = threshold for white mass (FA > wm\_threshold is considered white mass)
2. Also, specify the variables especially for running the SWMC algorithm:
   * t\_ini =  initial temperature (cannot be 0!)
   * t\_end = final temperature (must be > t\_ini!)
   * t\_num_it = number of iterations between initial and final temperature
3. Now, you can run the file. This will create a file in the folder "code/results/" in the format "results\_{id}.pkl". When the running is done, the {id} will be outputted.
4. To make the actual clustering we want to analyze the magnetization and susceptibility of the different temperatures in the MC algorithm. To do so, open the file "code/phase_analysis.py" and change the global variable "id" to the {id} belonging to your results and run the file to see the magnetization and susceptibility plot.
5. As described in the report, one must now search for a steep peak and a sudden decent. In between, one finds the superparamagnetic phase to use for the clustering.

### Clustering
1. To run the clustering algorithm, located in "code/swmc.py". At the top of the file there are a couple of variables which you can change. The most important one is the "type" variable, which should now be set to "clustering". 
2. After that, set the following variables:
   * q = num. of pot spin variables
   * mc\_iterations = num. of iterations per MC
   * mc\_burn\_in = number of burn-in samples for MC (must be < mc\_iterations!)
   * k\_neighbors = number of nearest neighbors
   * wm\_threshold = threshold for white mass (FA > wm\_threshold is considered white mass)
2. Also, specify the variables especially for running the SWMC algorithm:
   * t\_superp = temperature in superparamagnetic phase
   * Cij\_threshold = threshold for "core" clusters, section 4.3.2 of the Blatt paper
3. Now, you can run the file. This will create a file in the folder "code/results/" in the format "clustering\_{id}.pkl". When the running is done, the {id} will be outputted.
4. To get a view of what the clustering created looks like, one can use the file "code/plot\_result.py". In this file two global variables can be set, namely id, in which you can use the {id} outputted by the clustering in step 3, and the number of clusters that you want to see in the plot. Running will give you a plot as in the report.

### Executing in Euler
As mentioned in the report, the most computationally intensive steps of the code have been parallelized (package "joblib") in order to take advantage of additional processing cores. In our case, we have made use of the Euler cluster and have written a couple of scripts to make it even easier to use. Once you have connected to Euler:  
1. Set up the environment as menitioned above, but now only everything that is in "code/".  
2. Execute "source init\_euler.sh" in order to load the necessary modules and install the required python packages.  
3. Execute "source run\_swmc.sh" to submit the code inside "swmc.py" to the cluster.
