
![](LinuxBashROOT_logos.jpg) 




## Final project: Fully automated analysis of HIJING output

**Last update:** 20210701

HIJING (_Heavy Ion Jet INteraction Generator_) is a widely used Monte Carlo generator in high-energy proton-proton, proton-nucleus and nucleus-nucleus collisions. The physics incorporated in this model is based on QCD-inspired models for jets production, and includes multiple mini-jet production, soft excitation, nuclear shadowing of parton distribution functions and jet interaction in dense matter.

In this final project, you are challenged to use combined **Linux**, **Bash** and **ROOT** functionalities covered in the lecture, in order to fully automate the data analysis over one typical HIJING dataset, generated in 10 separate jobs on a local batch farm.

**Challenge #0: The dataset.** Download the compressed HIJING dataset (the compressed size is around 170 MB) from the following direct link: https://cernbox.cern.ch/index.php/s/BJern5Ky7ajoULd . From the terminal, you can download by using the command **wget**:

```bash
wget https://cernbox.cern.ch/index.php/s/BJern5Ky7ajoULd/download -O HIJING_LBF_test.tar.gz
```

After downloading, extract the dataset (the size will be around 680 MB after this step!) by executing

```bash
tar xf HIJING_LBF_test.tar.gz
```

This dataset corresponds to the HIJING model prediction for the collisions of heavy ions (Pb-Pb) at a collision energy of 2.76 TeV. That was the colliding system and energy in Run 1 operations (2009-2013) at Large Hadron Collider.

Inside the directory ```HIJING_LBF_test``` there are 10 subdirectories named ```0, 1, ..., 9```, and in each subdirectory 5 files. Each subdirectory corresponds to the working directory of a separate process that was running an independent HIJING simulation. Besides the various config or log files in each subdirectory, the most important file is ASCII file ```HIJING_LBF_test_small.out```, in which the final output of HIJING is stored. Each file ```HIJING_LBF_test_small.out``` contains the detailed output for 10 heavy-ion collisions. Therefore, the total dataset for the analysis in the final project amounts to 10x10 = 100 heavy-ion collisions. 

The file ```HIJING_LBF_test_small.out``` has the following example structure and content:
```bash
... some irrelevant header information ...

 BEGINNINGOFEVENT
           1       52443   780888.375             753         175         171   703661455
           1           221             0            11    0.147132292       -0.159234047        -4.90655518         4.94190931    
           2           331             0            11   -0.313573092       -0.375932842        -7.37812376         7.45607901    
           3           111             0             1    -6.50095940E-02    -8.47864971E-02   -0.190811187        0.256999820    
           4           211             0             1    0.497839928         7.74994195E-02   -0.866787255         1.01225448    
           5          -211             0             1   -0.753928840        0.411572814       -0.657382011         1.09061456    
           6           111             0             1    0.401703835       -0.226781890        0.116536394        0.494572222    
           7           321             0             1   -0.139996752         2.07054317E-02    0.166310146        0.539747834    
           8          -311             0            11     6.48042858E-02     3.16939428E-02     2.06294954E-02    0.503323913    
           9          -211             0             1    0.223366082        -1.79702844E-02    0.514695168        0.578458726    

... not showing 40k+ lines ...

       52434           130         52352             1    0.333040059       -0.420676589        -67.7665329         67.7704849    
       52435           310         52354             1   -0.198645145         2.50143819E-02    -47.0591469         47.0622101    
       52436           111         52378             1   -0.130700290       -0.176906317       -0.433586091        0.504579365    
       52437           111         52378             1     1.17687508E-02    -2.19080187E-02    -5.60622960E-02    0.148278296    
       52438           111         52378             1     4.29953672E-02     3.46547589E-02   -0.177609831        0.229825601    
       52439          -211         52390             1   -0.115880452       -0.135553151       -0.383471161        0.445355147    
       52440           211         52390             1    0.667943239       -0.345367700        -2.69137526         2.79793072    
       52441           310         52416             1   -0.294742197       -0.217407599         4.86402702         4.90311813    
       52442           310         52424             1     3.96723598E-02    0.293893129        -1.05792081         1.20617115    
       52443           310         52428             1   -0.235836297       -0.537985206        -21.5725250         21.5862579    
 BEGINNINGOFEVENT
           2       49425   758120.688             644         172         168  1821316801
           1          -213             0            11    0.433982253       -0.860428333        -13.9620905         14.0100384    
           2           113             0            11    0.609804869       -0.628097296        -7.59074020         7.68368912    
           3           211             0             1    0.105007850        -6.46358531E-04   -0.609461606        0.634002090    
           4          -213             0            11    0.421225607       -0.366191477        -11.0717869         11.1153040    
           5           223             0            11    -9.12701711E-02    0.117829926        -19.7575741         19.7740669    
           6           213             0            11    -5.82319610E-02    0.232945740        -11.1643286         11.1927309    
           7           223             0            11    0.786985457         4.14502472E-02    -16.2853432         16.3231220    
           8           331             0            11    0.749920487       -0.644374013        -7.72359276         7.84525061    
           9          -211             1             1    0.168157816         2.57476717E-02    -3.08301115         3.09085488    

... not showing 40k+ lines ...

       49416           211         49366             1    0.277626634         1.59957302        -99.0034943         99.0168991    
       49417           111         49366             1    0.569299519        0.941452265        -95.1648254         95.1712799    
       49418          -211         49367             1   -0.295076400        0.202438921        -62.0908966         62.0920868    
       49419           211         49367             1    -9.28900763E-03     5.50258160E-03    -15.6468039         15.6474295    
       49420           221         49367            11   -0.769395888        0.276162803        -161.382507         161.385498    
       49421            22         49384             1    0.230599046         8.52777585E-02   -0.653446436        0.698169351    
       49422            22         49384             1   -0.255980730       -0.116494276       -0.381368935        0.473855704    
       49423           211         49420             1   -0.277680546         8.05142373E-02    -34.2214851         34.2229919    
       49424          -211         49420             1   -0.280915141         2.66515389E-02    -66.7800903         66.7808304    
       49425           111         49420             1   -0.210800111        0.168997005        -60.3809128         60.3816643

... and so on for remaining events ...
```
The meaning of different entries above is as follows:  

  1. The beginning of data for each new event is marked with the tag **BEGINNINGOFEVENT**   

  2. In the very next line after the tag **BEGINNINGOFEVENT** is the event summary data (e.g. event number, the total number of particles, total energy, etc.)   

  3. After that line, each line holds information about individual particles. For instance, the entries in the line

     ```bash
     3           211             0             1    0.105007850        -6.46358531E-04   -0.609461606        0.634002090   
     ```

     have the following meaning:

     * ```3``` : particle label within a particular event      
     * ```211``` : PID, i.e. particle identity (211 = positively charged pion, -2212 = antiproton, etc.). To get the standardized PID code in high-energy physics for all particles, consult http://pdg.lbl.gov/2007/reviews/montecarlorpp.pdf      
     * ```0``` : this is the primary particle, i.e. this particle is not a product of resonance decay. Otherwise, this column indicates the label of the parent particle      
     * ```1``` : final or directly produced particle (alternatively, ```11``` in the 4th column indicates that this particle has decayed)    
     * ```0.105007850``` : _x_ component of momentum (in GeV/c)    
     * ```-6.46358531E-04``` : _y_ component of momentum (in GeV/c)  
     * ```-0.609461606``` : _z_ component of momentum (in GeV/c)     
     * ```0.634002090``` : particle energy (in GeV)   





**Challenge #1: Splitting.** Develop the script **Splitter.sh** which takes one argument, the top directory to your local HIJING dataset. That script splits in each of the subdirectories ```0, 1, ..., 9``` the large HIJING output file ```HIJING_LBF_test_small.out``` in 10 separate files named ```event_0.dat, ..., event_9.dat ```. Each of these new files contains the data only for a particular event.

At the end of this step, the situation in your local dataset needs to be schematically as follows:

```bash
<top-directory>/0:
HIJING_LBF_test_small.out
event_0.dat
...
event_9.dat

<top-directory>/1:
HIJING_LBF_test_small.out
event_0.dat
...
event_9.dat

...
...

<top-directory>/9:
HIJING_LBF_test_small.out
event_0.dat
...
event_9.dat
```

**Hint:** Use **grep -n BEGINNINGOFEVENT HIJING_LBF_test_small.out** to get the line numbers at which the entry for each new event begins. Then, programmatically extract those line numbers with **awk**. Finally, the output of **awk** use as an input to **sed** to split the file ```HIJING_LBF_test_small.out``` into 10 chunks, each chunk corresponding to the data of one event, something like:

```bash
sed -n 123,123456p HIJING_LBF_test_small.out > event_0.dat
```





**Challenge #2: Filtering.** Develop the script **Filter.sh** which takes one argument, the top directory to your local HIJING dataset. Then, this script filters in each of the subdirectories ```0, 1, ..., 9``` out of each new file ```event_?.dat``` obtained in the previous step only the information for the primary particles (i.e. particles with the label ```0``` in the 3rd column).

**Hint #1:** Collect all files ```event_?.dat``` with the command **find** and loop over them via **while+read**, something like:

```bash
while read File; do
 cd $(dirname $File) # go to the directory where the current file in the loop sits
 ... filter out the current file. Programmatically, its name is $(basename $File) ...
 cd - # go back
done < <(find <top-directory> -type f -name "event_*.dat")
```

**Hint #2:** The following example code snippet can do the actual filtering:

```bash
cp event_0.dat backup_0.dat 
awk '{if ($3 == 0) print $0}' < backup_0.dat 1>event_0.dat
```

If filtering went OK, clean up the temporary backup files.





**Challenge #3: Transferring.** Develop the script **Transfer.sh** which takes one argument, the top directory to your local HIJING dataset. This script is responsible to process all filtered files ```event_?.dat``` and store for each event for each particle its PID and kinematics (three components of momenta and energy) into **ROOT**'s container ```TTree```. Make one ```TTree``` container for each event, and then all ```TTree``` containers save in one common **ROOT** file named ```HIJING_LBF_test_small.root```, in each of the subdirectories ```0, 1, ..., 9```. After the transfer, clean up all files ```event_?.dat```.

At the end of this step, the situation in your local dataset needs to be schematically as follows:

```bash
<top-directory>/0:
HIJING_LBF_test_small.out
HIJING_LBF_test_small.root

<top-directory>/1:
HIJING_LBF_test_small.out
HIJING_LBF_test_small.root

...

<top-directory>/9:
HIJING_LBF_test_small.out
HIJING_LBF_test_small.root
```

**Hint:** As an example code snippet how to read external ASCII file directly into ```TTree```, and then save that container in the **ROOT** file, study the following toy example:

```c
// Example content of external file 'basic_1.dat' is formatted as "px py pz E", and it reads: 
/*
10 100 1000 10000
20 200 2000 20000
30 300 3000 30000
*/

// Example content of external file 'basic_2.dat' is formatted as "px py pz E", and it reads: 
/*
11 111 1111 11111
22 222 2222 22222
33 333 3333 33333
44 444 4444 44444
55 555 5555 55555
*/

// For a different format (i.e. number of columns!), you need to adapt: tree->ReadFile(filename,"px:py:pz:E"); used below (e.g. to add also PID entry)

#include "TFile.h"
#include "TTree.h"

int importASCIIfileIntoTTree(const char *filename)
{
 TFile *file = new TFile("output.root","update"); // open ROOT file named 'output.root', where TTree will be saved
 TTree *tree = new TTree("event","data from ascii file"); // make the new TTree for each event

 Long64_t nlines = tree->ReadFile(filename,"px:py:pz:E"); // whatever you specify here, will be relevant when you start later reading the TTree branches
 tree->Write(); // save TTree to 'output.root' file
 file->Close();
    
 return 0;   
}
```
Use then the above **ROOT** code snippet in the script in the following way:
```c
root -l -b -q importASCIIfileIntoTTree.C\(\"basic_1.dat\"\) // or abs-path to 'basic_1.dat', if macro and the file are not in the same directory
root -l -b -q importASCIIfileIntoTTree.C\(\"basic_2.dat\"\) // or abs-path to 'basic_2.dat', if macro and the file are not in the same directory
```

If you now inspect (e.g. with **ROOT**'s ```TBrowser```) the content of ```output.root``` file, you will see that it contains two ```TTree``` containers, one for the content of file ```basic_1.dat``` and another for ```basic_2.dat```. In reality, multiple events are stored in the same ```TTree``` container to optimize things further, but in this challenge that's not necessary.

From this point onward, only the dataset stored in ```TTree``` containers in the **ROOT** files is used in the final analysis.





**Challenge #4: Analysis.** Develop the script **Analysis.sh** which takes one argument, the top directory to your local HIJING dataset. This script is responsible to collect all **ROOT** files ```HIJING_LBF_test_small.root``` obtained in the previous step, and hand them over to dedicated **ROOT** macros for the final analysis. For the whole dataset, i.e. for all 100 heavy-ion collisions, this final script needs to produce:

1. figure (in 4 standard formats .pdf,.eps, .png and .C) holding the 3 histograms plotted side-by-side, with distributions of transverse momentum for pions, kaons and protons, respectively (to increase statistics, take that particles and antiparticles are the same). Transverse momentum is the Lorentz invariant quantity defined as: 
   $$
   p_T \equiv \sqrt{p_x^2 + p_y^2}
   $$
   
2. printouts in the terminal with the following format and content:

   ```bash  
   Average pT for the whole dataset:
   o pions   = ??? GeV/c
   o kaons   = ??? GeV/c   
   o protons = ??? GeV/c
   ```

**Hint #1:** To read entries from ```TTree```, have a look at the following code snippet (which is in sync with the formatting used in **Challenge #3**):

```c
// Example macro to read TTree from the ROOT file, and then all data from the TTree

int readDataFromTTree(const char *filename)
{

 TFile *file = new TFile(filename,"update"); // there are a few TTree's in this file, each corresponds to different event

 TList *lofk = file->GetListOfKeys(); // standard ROOT stuff, to read all entries in the ROOT file

 for(Int_t i=0; i<lofk->GetEntries(); i++)
 {
  TTree *tree = (TTree*) file->Get(Form("%s;%d",lofk->At(i)->GetName(),i+1)); // works if TTrees in ROOT file are named 'event;1', 'event;2'. Otherwise, adapt for your case

  if(!tree || strcmp(tree->ClassName(),"TTree")) // make sure the pointer is valid, and it points to TTree
  {
   cout<<Form("%s is not TTree!",lofk->At(i)->GetName())<<endl; 
   continue;
  }

  //tree->Print();  //from this printout, you can for instance inspect the names of the TTree's branches

  cout<<Form("Accessing TTree named: %s",tree->GetName())<<": "<<tree<<endl;
  Int_t nParticles = (Int_t)tree->GetEntries(); // number of particles
  cout<<Form("=> It has %d particles.",nParticles)<<endl;

  // Attach local variables to branches:
  Float_t px = 0., py = 0., pz = 0., E = 0.;
  tree->SetBranchAddress("px",&px); // that the name of this branch is px, you can inspect from tree->Print() above, and so on
  tree->SetBranchAddress("py",&py);
  tree->SetBranchAddress("pz",&pz);
  tree->SetBranchAddress("E",&E);

  for(Int_t p = 0; p < nParticles; p++) // loop over all particles in a current TTree
  {
   tree->GetEntry(p);
   cout<<Form("%d: %f %f %f %f",p,px,py,pz,E)<<endl;
  }  

  cout<<"Done with this event, marching on...\n"<<endl;  

 } // for(Int_t i=0; i<lofk->GetEntries(); i++)

 file->Close(); 

 return 0;
}
```

Use above macro for instance in the script **Analysis.sh** as:
```c
root -l readDataFromTTree.C\(\"output.root\"\)
```
Instead of trivially dumping on the screen the data via ```cout<<Form("%d: %f %f %f %f",p,px,py,pz,E)<<endl;``` expand this macro with the code which fills the histograms, and stores those histograms in the **ROOT** file ```ÀnalysisResults.root``` which contains the output results of your personal analysis. 

**Hint #2:** The 3 histograms (for pion, kaon and proton) need to be generated to hold the entries for the whole dataset of 100 events, not per event! So here you need to demonstrate that you can access histograms from the **ROOT** file, fill histograms with the new entries, and save them back updated to the **ROOT** file. This is a real-life example. At any point, in the **ROOT** file you need to have only 3 histograms, which you just access, fill and save updated for each event. To achieve that, please consider using the following standard strategy:

* open **ROOT** file (the global **ROOT** variable **gFile** points now to that particular file):

  ```c
  TFile *file = TFile::Open("path-to-ROOT-file","UPDATE");
  if(!file){return 1;}
  ```

* obtain the pointer to the histogram you need to update, something like:

  ```c
  TH1F *hist = dynamic_cast<TH1F*>(file->Get("hist-name"));
  ```

* fill the histogram the new entries from particular event

* save the updated histogram in the same **ROOT** file with

  ```c
  hist->Write(hist->GetName(),TObject::kSingleKey+TObject::kWriteDelete);
  ```

  This will write the updated histogram in the **ROOT** file to which the global **ROOT** variable **gFile** points to. Each time you open a new **ROOT** file, **gFile** is updated. The previous instance of that histogram is overwritten (see more details in the ROOT documentation at: https://root.cern.ch/doc/master/classTObject.html#a211e8b1ab4ef54a5f2ecbe809945fee8).

  Note that ownership is not affected, histogram is still owned by the very same **ROOT** file. On the other hand, you cannot access histogram from one file, update it and save it into another **ROOT** file in the same code straightforwardly, as here you have first to deal with the histogram ownership, which is always a bit tricky...

**Hint #3:** For the final plotting and printout, develop a separate standalone **ROOT** macro which only processes the final **ROOT** file ```ÀnalysisResults.root``` which contains the histograms of your personal analysis, and execute that macro at the end of script **Analysis.sh**.



**Challenge #5: The final touch.** Automate the whole procedure, i.e. the user (or in this case the examiner!) would like to execute your code as:

``` bash
source TheFinalTouch.sh <top-directory>
```

where the content of the script ```TheFinalTouch.sh``` is:

```bash
#!/bin/bash

[[ ! -d $1 ]] && echo "Not a valid directory!" || return 6

source Splitter.sh $1 && echo "Done with Splitter.sh" || return 5
source Filter.sh $1 && echo "Done with Filter.sh" || return 4
source Transfer.sh $1 && echo "Done with Transfer.sh" || return 3
source Analysis.sh $1 && echo "Done with Analysis.sh" || return 2

return 0
```



 