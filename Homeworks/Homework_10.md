![](bash_logo.png)

# Homework #10: Plotting and histogramming with ROOT

**Last update:** 20220728

**Challenge:** Implement a piece of code in the vanilla **ROOT** macro (just open the blank file, its content surround within ```{ ... }```, and save it as ```myFile.C```) which will treat the function $$f(x) = ax e^{-bx}$$ as a probability density function (p.d.f.) on the interval $$0 \leq x < 1$$ where $$x$$ is variable and $$a$$ and $$b$$ are parameters (see examples in http://root.cern.ch/root/html/TF1.html).

Set parameters $$a$$ and $$b$$ to be 0.1 and 0.4, respectively, and sample 50000 times variable $$x$$ from the implemented p.d.f. $$f(x)$$. Define one-dimensional histogram with the floating-point precision TH1F (https://root.cern.ch/doc/master/classTH1F.html) and fill it with all sampled values of $$x$$. 

1. What is the mean value of $$x$$?   
2. What is the 3rd moment of distribution (so-called skewness)?  

**Hint:** Do not calculate these quantities 'manually', instead use the dedicated member functions of histogram class, and just at the end of your **ROOT** macro print them on the screen.


