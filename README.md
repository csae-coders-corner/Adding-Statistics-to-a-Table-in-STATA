# Adding Statistics to a table in STATA

I recently asked CSAE colleagues about the best way of adding statistics to a table in Stata, and was surprised to hear that people were doing it in a few different ways, some more complicated than others. I wanted to share some of the useful advice I received on doing this with esttab and estadd, using a very simple example that adds the following statistics:

(i) the median of the control group; 

(ii) the beta coefficient, in terms of the standard deviation of the control group;

(iii) the p-value from a simple post-estimation t-test for coefficient equality. 


First, generate the median of the variable X1 for the control group:

  _pctile X1 if control==1, nq(10)

  scalar Median_ctrl = r(r5)

(Note that any percentile could have been stored e.g. r3 for the 30th percentile). Next, we also store the standard deviation of the control group as a scalar:

  su X1 if control==1

  scalar Stdev_ctrl = r(sd)

After the regression, we use estadd to store the results as a local macro name. 

  regress Y1 X1 X2

First, we store the previously-created median:

  local CtrlMed: display %4.3f Median_ctrl

  estadd local CtrlMed "`CtrlMed'"

Second, we store the estimated coefficient on X1, expressed in standard deviations of the control group:

  scalar TreatEffect = _b[X1]/Stdev_ctrl

  local TreatEffect: display %4.3f TreatEffect

  estadd local TreatEffect "`TreatEffect'"

Third, we store the p-value from a simple t-test of coefficient equality for X1 and X2:

  test X1=X2

  local CoeffEq: display %4.2f `r(p)'

  estadd local CoeffEq "`CoeffEq'"

Finally, we store the results and use them in esttab:

  estimates store StoreResults1

  local Statistics CtrlMed TreatmentEffect CoeffEq

  esttab StoreResults1, stats(`Statistics')



**AUTHOR: Muhammad Meki, Junior Research Fellow, Pembroke College, Oxford**

**14 January 2019**
