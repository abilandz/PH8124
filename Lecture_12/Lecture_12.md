![](../Common_Figures/LinuxBashROOT_logos.png)

# Lecture 12: ROOT - basic classes (Part 2/2)

**Last update**: 20220708

### Disclaimer
Here is a just a collection of code snippets which were used in the lecture &mdash; for the full description of the functionalities of **ROOT** classes in question, consult the official documentation:

* Overview of all tutorials: [https://root.cern/manual/](https://root.cern/manual/)

* Primer (for beginners): [https://root.cern/primer/](https://root.cern/primer/) (or [pdf](https://cernbox.cern.ch/index.php/s/bmbmbqUMA1keZCH) version)

* Users Guide (last update 2018, not maintained anymore): [html](https://root.cern.ch/root/htmldoc/guides/users-guide/ROOTUsersGuide.html) or [pdf](https://cernbox.cern.ch/index.php/s/N4k9AQ8LtCFWQIc) version


### Table of Contents
1. [Superimposing different plots](#superimposing)
2. [Playing with ROOT files](#root_files)
3. [TFileMerger](#tfilemerger)
4. [TTree](#ttree)



### 1. Superimposing different plots <a name="superimposing"></a>
Frequently, we want to show for instance two or more histograms (or graphs or functions) on the same canvas, superimposed on top of each other. The question then is which object will determine the common axes ranges. This problem is illustrated with the following code snippets:
```cpp
{
 TH1F *hist1 = new TH1F("hist1","title 1",10,0.,10.);
 hist1->SetLineColor(kRed);
 hist1->SetFillColor(kRed-10);
 hist1->Fill(4.44);

 TH1F *hist2 = new TH1F("hist2","title 2",100,0.,100.);
 hist2->SetLineColor(kBlue);
 hist2->SetFillColor(kBlue-10);
 hist2->Fill(22.22);
}
```
If we now add at then end:
```cpp
hist1->Draw();
hist2->Draw();
```
only the histogram 'hist2' is shown on the canvas. If we change the order, i.e.:
```cpp
hist2->Draw();
hist1->Draw();
```
only the histogram 'hist1' is shown. This indicates that the histogram we have drawn last by default overwrites what was drawn previously in the same default canvas. We can instead superimpose the current object on what is already plotted in the canvas, by passing argument ```"same"``` to member function ```Draw()```. For instance, if we use:
```cpp
hist2->Draw();
hist1->Draw("same");
```
then first histogram 'hist2' is plotted, and then 'hist1' is drawn on top of it. On the resulting superimposed figure, the axes ranges are being determined with the first object drawn in the canvas. Therefore, in order to prevent ordering problem, the convenient solution is always to have one dummy histogram with no content, just to set all common plotting information (e.g. axis ranges, axis titles, etc.). This strategy is illustrated with the following code:
```cpp
{
 // Define the dummy histogram just to set all common plotting thingies:
 TH1F *histStyle = new TH1F("histStyle","common title",20,0.,20.);
 histStyle->GetXaxis()->SetTitle("transverse momentum");
 histStyle->GetYaxis()->SetTitle("counts");
 histStyle->SetStats(kFALSE); // do not show stat box

 // Define some other histogram you want to plot:
 TH1F *hist1 = new TH1F("hist1","title 1",10,0.,10.);
 hist1->SetLineColor(kRed);
 hist1->SetFillColor(kRed-10);
 hist1->Fill(4.44);

 // Define some other object you want to plot:
 TF1 *f1 = new TF1("f1","exp(-x/4)",0.,15.); 
 f1->SetLineColor(kGreen+2);

 // Final plotting:
 TCanvas *c = new TCanvas("c","some canvas");
 histStyle->Draw();
 hist1->Draw("same");
 f1->Draw("same");

 // Final saving:
 c->SaveAs("superimposing.pdf"); 
}
```






### 2. Playing with ROOT files <a name="root_files"></a>
ROOT files are made in a very straightforward way by using class ```TFile```:
```cpp
{
 TFile *file = new TFile("someFileName.root","NEW");
}
```
This simple one-line code snippet created a new physical file on the hard disk, in the current working directory (otherwise in the first argument specify absolute path!), named ```someFileName.root```. If the file with that name already exists in the current working directory, that file will NOT be overwritten, because we have made a new flie with the option 'NEW'. The meaning of 4 most important options in ```TFile``` constructor is summarized here: 

* **NEW** or **CREATE** : Create a new file and open it for writing, if the file already exists the file is not opened.
* **RECREATE**	: Create a new file, if the file already exists it will be overwritten.
* **UPDATE** : Open an existing file for writing. If no file exists, it is created.
* **READ** : Open an existing file for reading (default).

But there is much more happening here behind the scene, when new ROOT file is being created. By design, ROOT has a global variable  ```gFile``` which is always initialized to the latest open file. This is illustrated in the following code snippet:
```cpp
{
 cout<<"0: "<<gFile<<endl;

 TFile *file_1 = new TFile("someFileName_1.root","RECREATE");
 cout<<"1: "<<gFile->GetName()<<endl;

 TFile *file_2 = new TFile("someFileName_2.root","RECREATE");
 cout<<"2: "<<gFile->GetName()<<endl;

 file_2->Close(); // close this file in memory
 cout<<"3: "<<gFile<<endl;
}
```
That being said, in order to save for instance histogram in the ROOT file, by using histogram's member functions, we need to know to which ROOT file the global variable ```gFile``` is initialized. This is illustrated with the following code snippet:
```cpp
{
 TFile *file = new TFile("someFileName.root","RECREATE");

 TH1F *hist = new TH1F("hist","title",10,0.,10.);
 hist->SetLineColor(kRed);
 hist->SetFillColor(kRed-10);
 hist->Fill(4.44);

 hist->Write(); // this saves the histogram to the file to which global variable 'gFile' points to

 file->Close();
}
```
The same principle works with the other classes we want to save in the ROOT file (e.g. ```TF1```, ```TGraphErrors```, etc.). The object appears in the ROOT file under its name (the first argument you have used in the constructor when declaring that object). Objects with the same name in the ROOT file get different cycle number.

Next, we discuss how to retrieve programmatically pointer to the object which was saved in the ROOT file, modify those objects, and then save them modified into another ROOT file. Let's assume that in the starting physical ROOT file named ```myFile.root``` (e.g. download it from: https://cernbox.cern.ch/index.php/s/Q6qVW5yVrIozAaa ) we have one TH1F object named 'hist' and TF1 object named 'fun'. First, we can inspect the file content with the following code snippet:
```cpp
{
 TFile *file = new TFile("myFile.root","READ");
 file->ls();
}
```
This produces the following example output:
```linux
TFile**		myFile.root	
 TFile*		myFile.root	
  KEY: TH1F	hist;1	title
  KEY: TF1	fun;1	cos(x)
```
We see clearly our two objects in this printout, let us now fetch their pointers programmatically, modify them, and saved modified in the new file, by using the following code snippet:
```cpp
{
 TFile *file = new TFile("myFile.root","READ");
 //file->ls(); // see the content of the file
 
 // Get pointer to histogram:
 TH1F *hist = dynamic_cast<TH1F*>(file->Get("hist"));
 hist->SetDirectory(0); // very important!! Remove the original default ownership!

 // Get pointer to function:
 TF1 *fun = dynamic_cast<TF1*>(file->Get("fun"));

 // Close the first file, as it is not needed any longer:
 file->Close();

 // Modify the objects, for instance:
 hist->SetLineColor(kBlue);
 hist->SetFillColor(kBlue-10);
 hist->Fill(1.44);
 fun->SetLineColor(kBlue);

 // Open the new file and save:
 TFile *fileNew = new TFile("myFileNew.root","RECREATE");
 hist->Write();
 fun->Write();

 // Close the new file:
 fileNew->Close();
}
```





### 3. TFileMerger <a name="tfilemerger"></a>
When part of the data is analyzed with one process, and another part with another process, the output ROOT files have exactly the same internal structure (e.g. number of histograms), only the histogram content is different. In situations like this, and in order to achieve the total statistics in the analysis, one is interested in merging (i.e. summing up) all histogram together. This can be achieved very conveniently with the ```TFileMerger``` class, which will merge automatically the content of all mergeable objects within the ROOT files (typically histograms and profiles).

As an important remark, we stress it out again that the ROOT files we want to merge must have exactly the same internal structure, otherwise weird things can happen, as ROOT cannot easily determine the internal structure of final merged file.

If in two subdirectories of your current working directory, named 10 and 11 let's say, you have file named 'mergeMe.root'
```linux
ls 10 11
10:
mergeMe.root

11:
mergeMe.root
```
we can merge them with the following code snippet:
```cpp
{
 TFileMerger *fileMerger = new TFileMerger(); 

 // Add files which need to be merged:
 fileMerger->AddFile("10/mergeMe.root",kFALSE);
 fileMerger->AddFile("11/mergeMe.root",kFALSE);

 // Final merging:
 fileMerger->OutputFile("merged.root");
 fileMerger->Merge();
}
```
The new file 'merged.root' has full statistics, which was initially fragmented in the files '10/mergeMe.root' and '11/mergeMe.root' summed up. This simple example can be trivially generalized for any number of histograms per ROOT file, and for any number of ROOT files. 





### 4. TTree <a name="ttree"></a>
ROOT uses a data container called ```TTree```to store raw data efficiently. Typically in high-energy physics, raw data is filtered out and only the most important information is stored in ```TTree``` class, usually one ```TTree``` per event (where event can be for instance one proton-proton or one heavy-ion collision and Large Hadron Collider). Then, few ```TTree```'s are being saved in one ROOT file. Therefore, the starting point of data analysis in high-energy physics amounts to opening ROOT files, and reading the data from ```TTree``` containers.

Here we provide code snippets how the raw data in ASCII files can be directly imported in ```TTree``` container, and saved in ROOT files. To generate some stream of raw data in the file ```someData.dat```, which consists of 1 million measurements of 4 quantities, we use the following construct:
```linux
for i in {1..1000000}; do echo {1..4}.$RANDOM; done > someData.dat
```
The content of the file ```someData.dat``` looks for instance as:
```bash
1.10247 2.11951 3.14323 4.23073
1.28850 2.23902 3.31925 4.29658
1.19320 2.1578 3.27001 4.25644
1.25195 2.12219 3.26179 4.22304
1.8306 2.27097 3.26353 4.9964
1.29283 2.7834 3.21447 4.24313
1.25895 2.3578 3.18258 4.3822
1.21078 2.16187 3.39 4.11658
1.854 2.21085 3.9341 4.12439
1.18069 2.10334 3.31529 4.7834
1.19776 2.29649 3.25466 4.15146
```
Let us assume that in this toy example the first three columns represent x, y, and z components of particle momenta, and the last column is particle energy. We can import that data in ```TTree``` and then save it in ROOT file named 'output.root' in the following way:
```cpp
#include "TFile.h"
#include "TTree.h"

void importASCIIfileIntoTTree(const char *filename)
{
 TFile *file = new TFile("output.root","recreate"); // open ROOT file named 'output.root', where TTree will be saved.
 TTree *tree = new TTree("chunk","data from ascii file"); // make new TTree

 Long64_t nlines = tree->ReadFile(filename,"px:py:pz:E"); // whatever you specify here, will be relevant when you start later reading the branches
 tree->Write(); // save TTree to 'output.root' file
 file->Close();
}
```
Then, just execute that macro either in interpreted or in compiled mode, for instance with:
```linux
root -l -b -q importASCIIfileIntoTTree.C\(\"someData.dat\"\)
```
It is instructive to compare the sizes of initial ASCII file, and the resulting ROOT file:
```linux
stat -c '%s' someData.dat 
30643925
stat -c '%s' output.root 
12142219
```
So almost factor 3 gain in size, even for such a simple example!

Finally, we provide the code snippet how to read data from ```TTree``` stored in ROOT file:
```cpp
// Example macro to read TTree from the file, and then all particles from the current TTree

void readDataFromTTree(const char *filename)
{

 TFile *file = new TFile("output.root","update"); // there multiple TTrees in this file, each corresponds to different event

 TList *lofk = file->GetListOfKeys();

 for(Int_t i=0; i<lofk->GetEntries(); i++)
 {

  TTree *tree = (TTree*) file->Get(Form("%s;%d",lofk->At(i)->GetName(),i+1)); // works if TTrees in ROOT file are named e.g. 'chunk;1', 'chunk;2'. Otherwise, adapt for your case

  if(!tree || strcmp(tree->ClassName(),"TTree")) // make sure the pointer is valid, and it points to TTree
  {
   cout<<Form("%s is not TTree!",lofk->At(i)->GetName())<<endl; 
   continue;
  }

  //tree->Print();  //from this printout, you can for instance inspect the names of the branches

  cout<<Form("Accessing TTree named: %s",tree->GetName())<<": "<<tree<<endl;
  Int_t nParticles = (Int_t)tree->GetEntries(); // number of particles
  cout<<Form("=> It has %d particles.",nParticles)<<endl;

  // Attach local variables to branches:
  Float_t px = 0., py = 0., pz = 0. , E = 0.;
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

}
```
Execute that code with:
```bash
root -l readDataFromTTree.C\(\"output.root\"\)
```




