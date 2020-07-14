![](LinuxBashROOT_logos.png)

# Lecture 11: ROOT - basic classes (Part 1/2)

**Last update**: 20200714

### Disclaimer
Here is a just a collection of code snippets used in the lecture, for the full description of the functionalities of **ROOT** classes in question, consult the official manual:

*  https://root.cern.ch/root/htmldoc/guides/users-guide/ROOTUsersGuide.html
*  https://cernbox.cern.ch/index.php/s/UPTygtTacSQVtOj (pdf)

### Table of Contents
1. [Interpreted 'Hello World' example in ROOT](#hello_int)
2. [Compiled 'Hello World' example in ROOT](#hello_comp)
3. [TGraphErrors](#TGraphErrors)
4. [TF1](#TF1)
5. [TF2](#TF2)
6. [TStopWatch](#TStopwatch)
7. [TH1F](#TH1F)
8. [TProfile](#TProfile)
9. [Cosmetics](#Cosmetics)






### 1. Interpreted 'Hello World' example in ROOT <a name="hello_int"></a>
Save in the file ```hello_Interpreted.C``` the following code snippet:
```cpp
{
 printf("Hello World!\n"); // C style
 cout<<"Hello World!"<<endl; // C++ style
}
```
Execute then the above code simply via:
```bash
root hello_Interpreted.C
```






### 2. Compiled 'Hello World' example in ROOT <a name="hello_int"></a>
Save in the file ```hello_Compiled.C``` the following code snippet:

```cpp
#include "Riostream.h"

Int_t helloCompiled()
{
 printf("Hello World!\n"); // C style
 cout<<"Hello World!"<<endl; // C++ style

 return 0;
}
```
Compile by using **ACLiC** either in the following way
```bash
root hello_Interpreted.C+
```
or
```bash
root hello_Interpreted.C++
```
**ACliC** is an interface which ensures that a machine independent ```C++``` compiler is used. By default, the same compiler and the compiler options are used which were used to compile the **ROOT** executable. The difference between appending ```++``` or ```+``` when compiling is that in the former case always all shared libraries are rebuilt from scratch.





### 3. TGraphErrors <a name="TGraphErrors"></a>
Imagine that you have ASCII file ```someData.dat``` with the following points:
```bash
0.5    4.440    0.0    0.01
1.5    3.123    0.0    0.02
2.5    2.111    0.0    0.01
3.5    1.211    0.0    0.03
4.5    2.561    0.0    0.04
5.5    3.432    0.0    0.05
```
The columns are interpreted by class ```TGraphErrors``` in the following way: _x_-value, _y_-value, error on _x_, error on _y_. The file can be processed and plotted automatically with the following code snippet:
```cpp
{
 TGraphErrors *ge = new TGraphErrors("someData.dat","%lg %lg %lg %lg"); 
 ge->Draw("ap"); 
}
```
If you use only two field specifiers in the constructor, ```%lg %lg```, then the first column is defaulted to _x_-values and the second one to _y_-values.

If on the other hand we have the data points stored in arrays within the code, then we need to use another constructor for ```TGraphErrors```, which can handle arrays as arguments:
```cpp
{
 Float_t x[6] = {0.5,1.5,2.5,3.5,4.5,5.5};
 Float_t y[6] = {4.440,3.123,2.111,1.211,2.561,3.432};
 Float_t ex[6] = {0.0};
 Float_t ey[6] = {0.01,0.02,0.01,0.03,0.04,0.05};

 TGraphErrors *ge = new TGraphErrors(6,x,y,ex,ey); 
 ge->Draw("ap"); 
}
```




### 4. TF1 <a name="TF1"></a>
This is a **ROOT** class to define 1-dimensional function, which can be used for instance as a probability density function (p.d.f.) for sampling.
```cpp
{
 TF1 *f1 = new TF1("f1","exp(-x*x)",-2.,2.);
 f1->Draw();
}
```
Thing to remember is that ```x``` has to be used to denote variable. In the case you want to introduce parameters, the notation ```[ ] ``` must be used for them:
```cpp
{
 TF1 *f1 = new TF1("f1","[0]*exp(-x*x) + [1]",-2.,2.);
 f1->SetParameter(0,4.123);
 f1->SetParameter(1,-2.123);
 f1->Draw();
}
```
In order to use ```TF1``` object as a p.d.f., we can perform the sampling as follows (**ROOT** takes automatically care of normalization!). For instance, to sample 10 random numbers from ```TF1``` we can use:
```cpp
 TF1 *f1 = new TF1("f1","exp(-x*x)",-2.,2.);
 for (Int_t i=0; i<10; i++)
 {
  cout<<f1->GetRandom()<<endl;
 }
```
This produces the following random sequence:
```cpp
1.97629
-0.690266
-0.404519
1.13079
-0.515687
-0.026516
1.20135
0.46183
0.0707625
0.452367
```
If we now re-execute the above code, we get exactly the same random sequence. We can make random sequence in general unique in time and space by inserting the following two lines of code at the beginning:
```cpp
{
 // Ensure that random sequence is unique in time and space:
 delete gRandom;
 gRandom = new TRandom3(0);
 
 // Do the sampling:
 TF1 *f1 = new TF1("f1","exp(-x*x)",-2.,2.);
 for (Int_t i=0; i<10; i++)
 {
  cout<<f1->GetRandom()<<endl;
 }
```
When doing a large scale Monte Carlo simulations, performance clearly matters, and it is preferred to do simulations in compiled mode. The version of above code snippet which can be saved in the file ```f1_random_compiled.C``` and compiled is:
```cpp
#include<Riostream.h>
#include<TF1.h>
#include<TRandom3.h>

Int_t f1_random_compiled()
{
 // Ensure that random sequence is unique in time and space:
 delete gRandom;
 gRandom = new TRandom3(0);

  // Do the sampling:
 TF1 *f1 = new TF1("f1","exp(-x*x)",-2.,2.);
 for (Int_t i=0; i<10; i++)
 {
  cout<<f1->GetRandom()<<endl;
 }

 return 0;
}
```
And you can compile and execute simply with:
```bash
root -l f1_random_compiled.C++
```

Let us now check the performance of interpreted vs. compiled mode.

* The following interpreted code is saved in the file ```f1_random_interpreted.C```:
```cpp
{
 delete gRandom;
 gRandom = new TRandom3(0);

 TF1 *f1 = new TF1("f1","exp(-x*x)",-2.,2.);
 for (Int_t i=0; i<1e8; i++)
 {
  f1->GetRandom();
 }
}
```
We time its execution with the **Bash** built-in command **time**:
```bash
time root -l -b -q f1_random_interpreted.C
root [0] 
Processing f1_random_interpreted.C...

real	0m13.081s
user	0m13.066s
sys	0m0.011s
```
* The following analogous compiled version is saved in the file ```f1_random_compiled.C```:
```cpp
#include<Riostream.h>
#include<TF1.h>
#include<TRandom3.h>

Int_t f1_random_compiled()
{
 // Ensure that random sequence is unique in time and space:
 delete gRandom;
 gRandom = new TRandom3(0);

 // Do the sampling:
 TF1 *f1 = new TF1("f1","exp(-x*x)",-2.,2.);
 for (Int_t i=0; i<1e8; i++)
 {
  f1->GetRandom();
 }

 return 0;
}
```
We now time its execution in the same way:

```bash
time root -l -b -q f1_random_compiled.C++
root [0] 
Processing f1_random_compiled.C++...
Info in <TUnixSystem::ACLiC>: creating shared library /home/abilandz/Lecture/SS2019/Lecture_11/Examples/./f1_random_compiled_C.so
(Int_t)0

real	0m6.130s
user	0m6.004s
sys	0m0.133s
```
In above examples, we have used 3 frequently used flags for **ROOT** with the following meaning:

* -l : switch off the splash screen at the beginning
* -b : run **ROOT** in the batch mode
* -q : exit **ROOT** upon execution






### 5. TF2 <a name="TF2"></a>
**ROOT** supports also multivariate functions and p.d.f.'s, we here discuss explicitly 2D case, and the rest can be achieved by analogy.

```cpp
{
 // Make random sequence unique in tiem and space:
 delete gRandom;
 gRandom = new TRandom3(0);

 // Define and onfigure the 2D f(x,y):
 TF2 *f2 = new TF2("f2","[0]*x + [1]*y",-1,1,100,1000);
 f2->SetParameter(0,4.1234);
 f2->SetParameter(1,2.1234);

 // Do the sampling:
 Double_t var1 = 0.;
 Double_t var2 = 0.;
 for(Int_t i=0; i<10; i++)
 {
  f2->GetRandom2(var1,var2);
  cout<<var1<<", "<<var2<<endl;
 }
}
```
The thing to note is that 2 variables need to be declared first, and then their values are being randomly updated with the call to member function ```f2->GetRandom2(var1,var2);```. Everything else is analogous to the 1D case.





### 6. TStopWatch <a name="TStopWatch"></a>
**ROOT** has its own class for timing, it is used in the following way:

```cpp
{
 TStopwatch watch;
 watch.Start();

 // some code here

 watch.Stop();
 watch.Print();
}
```






### 7. TH1F <a name="TH1F"></a>
To illustrate histogramming in ROOT, we use 1 dimensional histogram class with the floating point precision, ```TH1F```.  Other supported classes are for instance ```TH1I``` (for integers), ```TH1D``` (for doubles), etc. Corresponding classes exist for 2D and 3D, e.g. ```TH2F``` and ```TH3F```. For even higher number of dimensions, there exists a fancy class ```THnSparse```. 

Histogramming is illustrated in the following example:
```cpp
{
 // Define some p.d.f. for sampling:
 TF1 *funct = new TF1("funct","exp(-x*x)",-2.,2.);

 // Define some histogram to store the results of sampling:
 TH1F *hist = new TH1F("hist","hist title",100,-2.,2.);

 // Fill the histogram with 10000 sampled points from pre-define p.d.f:
 hist->FillRandom("funct",10000);

 // Plot both the starting p.d.f. and resulting histogram:
 TCanvas *c = new TCanvas("c","canvas title",1400,700); // define a new 1400x700 canvas
 c->Divide(2,1); // divide horizontal axis in two, and vertical leave intact 
 
 c->cd(1); // move to the first pad after canvas subdivision resulting from call to Divide()
 funct->Draw();
 
 c->cd(2); // move to the second pad after canvas subdivision resulting from call to Divide()
 hist->Draw();
 
 // Save the canvas:
 c->SaveAs("histExample.pdf"); 
 c->SaveAs("histExample.eps"); 
 c->SaveAs("histExample.png"); 
 c->SaveAs("histExample.C"); 
}
```






### 8. TProfile <a name="TProfile"></a>
```TProfile``` class is a special histogram class, with double precision, which instead of plotting the entire distributions focuses only on average values. This class has a rather neat use cases when our observables of interest are all-event averages. 

Its usage is illustrated in the following example:
```cpp
{
 // Define some p.d.f. for sampling:
 TF1 *funct = new TF1("funct","exp(-x*x)",-2.,2.);

 // Define some histogram to store the results of sampling:
 TH1F *hist = new TH1F("hist","hist title",100,-2.,2.);

 // Fill the histogram with 10000 sampled points from pre-define p.d.f:
 hist->FillRandom("funct",10000);

 // Define some profile to store only the average values of sampling in each interval:
 TProfile *pro = new TProfile("pro","profile: #LTx^{3}#GT vs. x",10,-2.,2.);

 // Fill the profile with 10000 sampled points from pre-define p.d.f, to get <x^3> for each bin:
 for(Int_t i=0; i<10000; i++)
 {
  Double_t value = funct->GetRandom();
  pro->Fill(value,pow(value,3.)); // <x^3> vs. x
 }

 // Plot both the starting p.d.f. and resulting histogram:
 TCanvas *c = new TCanvas("c","canvas title",2100,700); // define a new 1400x700 canvas
 c->Divide(3,1); // divide horizontal axis in two, and vertical leave intact 

 c->cd(1); // move to the first pad after canvas subdivision resulting from call to Divide()
 funct->Draw();

 c->cd(2); // move to the second pad after canvas subdivision resulting from call to Divide()
 hist->Draw();

 c->cd(3); // move to the third pad after canvas subdivision resulting from call to Divide()
 pro->Draw();

 // Save the canvas:
 c->SaveAs("profileExample.pdf"); 
 c->SaveAs("profileExample.eps"); 
 c->SaveAs("profileExample.png"); 
 c->SaveAs("profileExample.C"); 
}
```






### 9. Cosmetics <a name="Cosmetics"></a>
To change the style, colour, size, etc. of lines, markers, etc. ROOT provides the following example attribute classes:

* TAttLine
* TAttMarker
* TAttFill
* TColor

For instance, in the previous example, you could have changed the histogram plotting appearance with:
```cpp
{
 hist->SetLineColor(kRed); 
 hist->SetLineStyle(2); 
 hist->SetLineWidth(4); 

 hist->SetMarkerColor(kRed); 
 hist->SetMarkerStyle(kCircle);
 hist->SetMarkerSize(1.4);
} 
```
The above functions can be called also for the classes ```TF1```, ```TGraphErrors```, etc. 

For instance, used in a concrete example:

```cpp
{
 // Define some p.d.f. for sampling:
 TF1 *funct = new TF1("funct","exp(-x*x)",-2.,2.);
 funct->SetLineColor(kBlue);

 // Define some histogram to store the results of sampling:
 TH1F *hist = new TH1F("hist","hist title",100,-2.,2.);
 hist->SetFillColor(kBlue-10); 

 // Fill the histogram with 10000 sampled points from pre-define p.d.f:
 hist->FillRandom("funct",10000);

 // Define some profile to store only the average values of sampling in each interval:
 TProfile *pro = new TProfile("pro","profile: #LTx^{3}#GT vs. x",10,-2.,2.);
 pro->SetMarkerStyle(kFullCircle);
 pro->SetMarkerColor(kRed);
 pro->SetLineColor(kRed);

 // Fill the profile with 10000 sampled points from pre-define p.d.f, to get <x^3> for each bin:
 for(Int_t i=0; i<10000; i++)
 {
  Double_t value = funct->GetRandom();
  pro->Fill(value,pow(value,3.)); // <x^3> vs. x
 }

 // Plot both the starting p.d.f. and resulting histogram:
 TCanvas *c = new TCanvas("c","canvas title",2100,700); // define a new 1400x700 canvas
 c->Divide(3,1); // divide horizontal axis in two, and vertical leave intact 

 c->cd(1); // move to the first pad after canvas subdivision resulting from call to Divide()
 funct->Draw();

 c->cd(2); // move to the second pad after canvas subdivision resulting from call to Divide()
 hist->Draw();

 c->cd(3); // move to the third pad after canvas subdivision resulting from call to Divide()
 pro->Draw();

 // Save the canvas:
 c->SaveAs("profileCosmeticsExample.pdf"); 
 c->SaveAs("profileCosmeticsExample.eps"); 
 c->SaveAs("profileCosmeticsExample.png"); 
 c->SaveAs("profileCosmeticsExample.C"); 
}
```

Another direction where ROOT is very powerful is a wide support for drawing options, documented in the following example class:

* TGraphPainter
* THistPainter

Its usage is illustrated in the following code snippet
```cpp
{
 TH2D *hist = new TH2D("hist","title",100,-10.,10.,50,0,10.);
 for(Int_t i=0; i<10000; i++)
 {
  hist->Fill(gRandom->Gaus(0,2),gRandom->Exp(1.5));
 }
 hist->Draw("surf3");
}
```