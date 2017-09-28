Behavior
--------

### Experimental Design

``` r
APA$genoAPA <- as.factor(paste(APA$genotype,APA$APA, sep="_"))
summary(APA)
```

    ##       ind        genotype         APA                  genoAPA  
    ##  BL 1   : 1   WT     :19   untrained:14   FMR1-KO_trained  : 5  
    ##  BL 10  : 1   FMR1-KO:11   trained  :16   FMR1-KO_untrained: 6  
    ##  BL 11  : 1                               WT_trained       :11  
    ##  BL 12  : 1                               WT_untrained     : 8  
    ##  BL 13  : 1                                                     
    ##  BL 14  : 1                                                     
    ##  (Other):24

### Avoidance behavior: number of entrances

``` r
wtfmr1NumSummary %>%
  ggplot(aes(x= sessionNum, y=m, shape=genotype)) + 
  geom_errorbar(aes(ymin=m-se, ymax=m+se, color=genoAPA), width=.1) +
  geom_line(aes(colour=genoAPA, linetype=genoAPA)) +
  geom_point(size = 2.5, aes(colour=genoAPA, shape=genotype)) +
  scale_color_manual(values = colvalAPA12) +
  scale_y_continuous(name="Number of Entrances",
                     limits = c(0,35)) +
  scale_x_continuous(name = NULL, 
                     breaks = c(1, 2, 3, 4, 5, 6),
                     labels=c("1" = "Hab. ", "2" = "T1", "3" = "T2", 
                              "4" = "T3", "5" = "Retest", "6" = "Reten.")) +
  theme_cowplot(font_size = 8, line_size = 0.25) +
  background_grid(major = "y", minor = "y") +
  scale_linetype_manual(values = c(1,2,1,2)) +
  scale_shape_manual(values=c(16, 1))
```

![](../results/1-numentrances-1.png)

``` r
wtfmr1NumSummary$behvaior <- "Number of Entrances"
numentrances <- wtfmr1NumSummary %>%
  ggplot(aes(x= sessionNum, y=m, shape=genotype)) + 
  geom_errorbar(aes(ymin=m-se, ymax=m+se, color=genoAPA), width=.1) +
  geom_line(aes(colour=genoAPA, linetype=genoAPA)) +
  geom_point(size = 2.5, aes(colour=genoAPA, shape=genotype)) +
  scale_color_manual(values = colvalAPA12) +
  scale_y_continuous(name="Number of Entrances",
                     limits = c(0,35)) +
  scale_x_continuous(name = NULL, 
                     breaks = c(1, 2, 3, 4, 5, 6),
                     labels=c("1" = "Hab. ", "2" = "T1", "3" = "T2", 
                              "4" = "T3", "5" = "Retest", "6" = "Reten.")) +
  theme_cowplot(font_size = 8, line_size = 0.25) +
  background_grid(major = "y", minor = "y") +
  theme( legend.position="none",
         axis.title.y = element_blank()) +
  scale_linetype_manual(values = c(1,2,1,2)) +
  scale_shape_manual(values=c(16, 1)) +
  facet_wrap(~behvaior)
numentrances
```

![](../results/1-numentrances-2.png)

``` r
pdf(file="../figures/1-numentrances.pdf", width=2.25, height=2)
plot(numentrances)
dev.off()
```

    ## quartz_off_screen 
    ##                 2

Electrophysiology
-----------------

### IO Max

``` r
summary_long %>%
  filter(grepl("IO_Max", variable))%>% 
  filter(!grepl("LTP_Baseline_SD", variable))%>% 
  ggplot(aes(x=genoAPA, y=value, color=genoAPA, fill=genoAPA)) +
  geom_violin() +  
  facet_wrap(~variable, scales = "free_y") +
  theme_bw() +
  scale_y_continuous(name=NULL) + 
  scale_x_discrete(name=NULL) + 
  scale_colour_manual(values=colvalAPA12) +
  theme_bw() + 
  theme(panel.grid.minor = element_blank(),
        axis.text.x = element_blank(), 
        axis.ticks = element_blank())  +
  scale_fill_manual(values=colorvalgenoAPA2)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](../results/2-io_max-1.png)

``` r
iomax <- summary_long %>%
  filter(grepl("IO_Max", variable))%>% 
  ggplot(aes(x=genoAPA, y=value, color=genoAPA, fill=genoAPA)) +
  geom_violin() +  
  facet_wrap(~variable, scales = "free_y") +
  theme_bw() +
  scale_y_continuous(name=NULL) + 
  scale_x_discrete(name=NULL) + 
  scale_colour_manual(values=colvalAPA12) +
  theme_cowplot(font_size = 8, line_size = 0.1) +
  theme(panel.grid.minor = element_blank(),
        axis.text.x = element_blank(), 
        legend.position="none",
        axis.title = element_blank())  +
  scale_fill_manual(values=colorvalgenoAPA2) 
iomax
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](../results/2-io_max-2.png)

``` r
pdf(file="../figures/2-iomax.pdf", width=1.25, height=2)
plot(iomax)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

``` r
dev.off()
```

    ## quartz_off_screen 
    ##                 2

    ##       dna    cq   gene
    ## 1 0.19531 34.22 cam2kd
    ## 2 0.19531 34.44 cam2kd
    ## 3 0.19531 34.61 cam2kd
    ## 4 0.39063 32.73 cam2kd
    ## 5 0.39063 32.98 cam2kd
    ## 6 0.39063 32.70 cam2kd

![](../results/qpcrdatawrangle-1.png)

``` r
## Analyzing just the  CA1 data ----
CA1only <- dplyr::filter(qpcr, APA != "home", region == "CA1") %>%
  droplevels()
CA1only <- CA1only[1:12]

dd_CA1only <- cq2counts(data=CA1only, genecols=c(10:12), condcols=c(1:9), effic=eff)

naive_dd_CA1only <- mcmc.qpcr(
  data=dd_CA1only,
  fixed="APA",
  pr=T,pl=T, singular.ok=TRUE)
```

    ## $PRIOR
    ## $PRIOR$R
    ## $PRIOR$R$V
    ##      [,1] [,2] [,3]
    ## [1,]    1    0    0
    ## [2,]    0    1    0
    ## [3,]    0    0    1
    ## 
    ## $PRIOR$R$nu
    ## [1] 2.002
    ## 
    ## 
    ## $PRIOR$G
    ## $PRIOR$G$G1
    ## $PRIOR$G$G1$V
    ## [1] 1
    ## 
    ## $PRIOR$G$G1$nu
    ## [1] 0
    ## 
    ## 
    ## 
    ## 
    ## $FIXED
    ## [1] "count~0+gene++gene:APA"
    ## 
    ## $RANDOM
    ## [1] "~sample"
    ## 
    ## 
    ##                        MCMC iteration = 0
    ## 
    ##  Acceptance ratio for liability set 1 = 0.000516
    ## 
    ##  Acceptance ratio for liability set 2 = 0.000483
    ## 
    ##  Acceptance ratio for liability set 3 = 0.000300
    ## 
    ##                        MCMC iteration = 1000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.436286
    ## 
    ##  Acceptance ratio for liability set 2 = 0.430337
    ## 
    ##  Acceptance ratio for liability set 3 = 0.260700
    ## 
    ##                        MCMC iteration = 2000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.437901
    ## 
    ##  Acceptance ratio for liability set 2 = 0.440371
    ## 
    ##  Acceptance ratio for liability set 3 = 0.320789
    ## 
    ##                        MCMC iteration = 3000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.441385
    ## 
    ##  Acceptance ratio for liability set 2 = 0.442865
    ## 
    ##  Acceptance ratio for liability set 3 = 0.335256
    ## 
    ##                        MCMC iteration = 4000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.417769
    ## 
    ##  Acceptance ratio for liability set 2 = 0.405764
    ## 
    ##  Acceptance ratio for liability set 3 = 0.328722
    ## 
    ##                        MCMC iteration = 5000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.414945
    ## 
    ##  Acceptance ratio for liability set 2 = 0.410573
    ## 
    ##  Acceptance ratio for liability set 3 = 0.325922
    ## 
    ##                        MCMC iteration = 6000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.407923
    ## 
    ##  Acceptance ratio for liability set 2 = 0.409528
    ## 
    ##  Acceptance ratio for liability set 3 = 0.328589
    ## 
    ##                        MCMC iteration = 7000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.412934
    ## 
    ##  Acceptance ratio for liability set 2 = 0.404551
    ## 
    ##  Acceptance ratio for liability set 3 = 0.330756
    ## 
    ##                        MCMC iteration = 8000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.412110
    ## 
    ##  Acceptance ratio for liability set 2 = 0.404618
    ## 
    ##  Acceptance ratio for liability set 3 = 0.327244
    ## 
    ##                        MCMC iteration = 9000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.417055
    ## 
    ##  Acceptance ratio for liability set 2 = 0.405539
    ## 
    ##  Acceptance ratio for liability set 3 = 0.329167
    ## 
    ##                        MCMC iteration = 10000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.421176
    ## 
    ##  Acceptance ratio for liability set 2 = 0.403034
    ## 
    ##  Acceptance ratio for liability set 3 = 0.327689
    ## 
    ##                        MCMC iteration = 11000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.415725
    ## 
    ##  Acceptance ratio for liability set 2 = 0.407910
    ## 
    ##  Acceptance ratio for liability set 3 = 0.327644
    ## 
    ##                        MCMC iteration = 12000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.414802
    ## 
    ##  Acceptance ratio for liability set 2 = 0.403382
    ## 
    ##  Acceptance ratio for liability set 3 = 0.329900
    ## 
    ##                        MCMC iteration = 13000
    ## 
    ##  Acceptance ratio for liability set 1 = 0.412264
    ## 
    ##  Acceptance ratio for liability set 2 = 0.407742
    ## 
    ##  Acceptance ratio for liability set 3 = 0.327589

``` r
diagnostic.mcmc(model=naive_dd_CA1only, col="grey50", cex=0.8)

dd_CA1only %>%  
  ggplot(aes(x=genoAPA, y=count)) + 
  geom_violin(aes(fill=genoAPA, color=genoAPA)) +  scale_y_log10(name="log10(gene counts)") +
  facet_wrap(~gene, scales = "free_y") +
  scale_x_discrete(name="") +
  theme_cowplot(font_size = 8, line_size = 0.25) +
  theme(axis.text.x = element_blank(), 
        strip.text = element_text(face = "italic")) + 
  scale_color_manual(values = colvalAPA12) +
  scale_fill_manual(values = colorvalgenoAPA2) 
```

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 27 rows containing non-finite values (stat_ydensity).

``` r
CA1genes <- dd_CA1only %>%  
  ggplot(aes(x=genoAPA, y=count)) + 
  geom_violin(aes(fill=genoAPA, color=genoAPA)) +  scale_y_log10(name="gene counts") +
  facet_wrap(~gene, scales = "free_y") +
  scale_x_discrete(name="") +
  theme_cowplot(font_size = 8, line_size = 0.25) +
  theme(axis.text.x = element_blank(), 
        strip.text = element_text(face = "italic"),
        legend.position = "none") + 
  scale_color_manual(values = colvalAPA12) +
  scale_fill_manual(values = colorvalgenoAPA2) 
CA1genes
```

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 27 rows containing non-finite values (stat_ydensity).

``` r
pdf(file="../figures/3-CA1genes.pdf", width=4.25, height=2)
plot(CA1genes)
```

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 27 rows containing non-finite values (stat_ydensity).

``` r
dev.off()
```

    ## quartz_off_screen 
    ##                 2

Integration across levels
-------------------------

    ##    ind genotype     APA    genoAPA               measure value
    ## 1 BL 1       WT trained WT_trained pretraining_Entrances    32
    ## 2 BL 1       WT trained WT_trained   training1_Entrances     8
    ## 3 BL 1       WT trained WT_trained   training2_Entrances     3
    ## 4 BL 1       WT trained WT_trained   training3_Entrances     2
    ## 5 BL 1       WT trained WT_trained      retest_Entrances     2
    ## 6 BL 2       WT trained WT_trained pretraining_Entrances    35

    ##    ind genotype       APA      genoAPA                measure  value
    ## 1 BL 1       WT   trained   WT_trained TotalLocomotorActivity  98.40
    ## 2 BL 2       WT   trained   WT_trained TotalLocomotorActivity 108.41
    ## 3 BL 3       WT   trained   WT_trained TotalLocomotorActivity  97.90
    ## 4 BL 4       WT untrained WT_untrained TotalLocomotorActivity 109.33
    ## 5 BL 5       WT   trained   WT_trained TotalLocomotorActivity  97.36
    ## 6 BL 6       WT untrained WT_untrained TotalLocomotorActivity 113.41

    ##     ind genotype      genoAPA       APA measure     value
    ## 1  BL 1       WT   WT_trained   trained    grim 2.0000000
    ## 2 BL 10       WT WT_untrained untrained    grim 0.3333333
    ## 3 BL 11       WT   WT_trained   trained    grim 3.3333333
    ## 4 BL 12       WT WT_untrained untrained    grim 2.0000000
    ## 5 BL 13       WT   WT_trained   trained    grim 6.6666667
    ## 6 BL 14       WT WT_untrained untrained    grim 3.6666667

    ## Warning in bind_rows_(x, .id): Unequal factor levels: coercing to character

    ## Warning in bind_rows_(x, .id): Unequal factor levels: coercing to character

    ## Warning in bind_rows_(x, .id): Unequal factor levels: coercing to character

    ## Warning in bind_rows_(x, .id): Unequal factor levels: coercing to character

``` r
all$correlation1 <- "Entrances vs IO_Max"
correlation <- all %>%
  filter(APA == "trained") %>%
  ggplot(aes(x=training3_Entrances, y=IO_Max, color=genoAPA)) +
  geom_point(size = 1.5, aes(colour=genoAPA, shape=genotype)) +
  geom_smooth(method = lm, alpha = 0.2, aes(linetype=genoAPA)) +
  theme_cowplot(font_size = 8, line_size = 0.1) +
  #theme(legend.position="none") +
  scale_color_manual(values = c("red", "red")) +
  scale_linetype_manual(values = c(1,2)) +
  scale_shape_manual(values=c(16, 1)) +
  facet_wrap(~correlation1)
correlation
```

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](../results/integrationfigures-1.png)

``` r
pdf(file="../figures/3-correlation1.pdf", width=3.25, height=2)
plot(correlation)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

``` r
dev.off()
```

    ## quartz_off_screen 
    ##                 2

``` r
all$correlation2 <- "Entrances vs rpl19"
correlation <- all %>%
  filter(APA == "trained") %>%
  ggplot(aes(x=training3_Entrances, y=log10(rpl19), color=genoAPA)) +
  geom_point(size = 1.5, aes(colour=genoAPA, shape=genotype)) +
  geom_smooth(method = lm, alpha = 0.2, aes(linetype=genoAPA)) +
  theme_cowplot(font_size = 8, line_size = 0.1) +
  theme(legend.position="none",
        axis.title = element_blank()) +
  scale_color_manual(values = c("red", "red")) +
  scale_linetype_manual(values = c(1,2)) +
  scale_shape_manual(values=c(16, 1)) +
  facet_wrap(~correlation2)
correlation
```

    ## Warning: Removed 1 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](../results/integrationfigures-2.png)

``` r
pdf(file="../figures/3-correlation2.pdf", width=3, height=2)
plot(correlation)
```

    ## Warning: Removed 1 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 1 rows containing missing values (geom_point).

``` r
dev.off()
```

    ## quartz_off_screen 
    ##                 2

``` r
## then create a matrix of just gene expression data with NAs ommited
allmatrix <- all # prepare for matrix
allmatrix$genoAPAind <- as.factor(paste(allmatrix$genoAPA,allmatrix$ind, sep="_"))
rownames(allmatrix) <- allmatrix$genoAPAind  # set $genoAPAsession as rownames
names(allmatrix)
```

    ##  [1] "ind"                    "genotype"              
    ##  [3] "APA"                    "genoAPA"               
    ##  [5] "grim"                   "IO_Max"                
    ##  [7] "LastLocomotorActivity"  "LastPunishment"        
    ##  [9] "LastTotalEntrances"     "LTP_Baseline"          
    ## [11] "LTP_Baseline_SD"        "pretraining_Entrances" 
    ## [13] "prkcz"                  "Punishment.1"          
    ## [15] "retention_Entrances"    "retest_Entrances"      
    ## [17] "rpl19"                  "T1Retention"           
    ## [19] "T2Retention"            "TotalLocomotorActivity"
    ## [21] "TotalPunishment"        "training1_Entrances"   
    ## [23] "training2_Entrances"    "training3_Entrances"   
    ## [25] "correlation1"           "correlation2"          
    ## [27] "genoAPAind"

``` r
allmatrix <- allmatrix[-c(1:4,7:12,14:16,18:21,25:27)]  ## remove non-numeric columns
allmatrix <- log10(allmatrix + 1) #log transform all data
allmatrix <- as.matrix(allmatrix)
allmatrix <- na.omit(allmatrix)
allmatrix_cor <- round(cor(allmatrix),2)  # then create a correlation matrix

corrplot(allmatrix_cor, type="lower", tl.col="black", tl.srt=45)
```

![](../results/correlationmatrix-1.png)

``` r
allmatrix <- all %>%
  filter(genotype == "WT", APA == "trained")
allmatrix$genoAPAind <- as.factor(paste(allmatrix$genoAPA,allmatrix$ind, sep="_"))
rownames(allmatrix) <- allmatrix$genoAPAind  # set $genoAPAsession as rownames
allmatrix <- allmatrix[-c(1:4,7:12,14:16,18:21,25:27)]  ## remove non-numeric columns
allmatrix <- log10(allmatrix + 1) #log transform all data
allmatrix <- as.matrix(allmatrix)
allmatrix <- na.omit(allmatrix)
allmatrix_cor <- round(cor(allmatrix),2)  # then create a correlation matrix

corrplot(allmatrix_cor, type="lower", tl.col="black", tl.srt=45)
```

![](../results/correlationmatrix-2.png)

``` r
allmatrix <- all %>%
  filter(genotype == "FMR1-KO", APA == "trained")
allmatrix$genoAPAind <- as.factor(paste(allmatrix$genoAPA,allmatrix$ind, sep="_"))
rownames(allmatrix) <- allmatrix$genoAPAind  # set $genoAPAsession as rownames
allmatrix <- allmatrix[-c(1:4,7:12,14:16,18:21,25:27)]  ## remove non-numeric columns
allmatrix <- log10(allmatrix + 1) #log transform all data
allmatrix <- as.matrix(allmatrix)
allmatrix <- na.omit(allmatrix)
allmatrix_cor <- round(cor(allmatrix),2)  # then create a correlation matrix

corrplot(allmatrix_cor, type="upper", tl.col="black", tl.srt=45)
```

![](../results/correlationmatrix-3.png)

``` r
## with all long
alllongslim <- alllong %>%
  filter(grepl("IO_Max|pretraining_Entrances|training3_Entrances|grim|prkcz|rpl19", measure)) 

alllongslim$measure <- factor(alllongslim$measure, levels = c("pretraining_Entrances", "training3_Entrances", "IO_Max", "grim", "prkcz", "rpl19"))


behaviorfigure  <- alllongslim %>%
  filter(grepl("training3_Entrances| ", measure)) %>%
  ggplot(aes(x=genoAPA, y=value, color=genoAPA, fill=genoAPA)) +
  geom_violin() +  
  facet_wrap(~measure, scales = "free_y") +
  theme_bw() +
  scale_y_continuous(name=NULL) + 
  scale_x_discrete(name=NULL) + 
  scale_colour_manual(values=colvalAPA12) +
  theme_cowplot(font_size = 8, line_size = 0.1) +
  theme(panel.grid.minor = element_blank(),
        axis.text.x = element_blank(), 
        legend.position="none",
        axis.title = element_blank())  +
  scale_fill_manual(values=colorvalgenoAPA2) 
behaviorfigure
```

![](../results/newintegrativefigures-1.png)

``` r
pdf(file="../figures/4-behaviorfigure.pdf", width=1.25, height=2)
plot(behaviorfigure)
dev.off()
```

    ## quartz_off_screen 
    ##                 2

``` r
qpcrfigure <- alllongslim %>%
  filter(grepl("grim|prkcz|rpl19| ", measure)) %>%
  ggplot(aes(x=genoAPA, y=log10(value), color=genoAPA, fill=genoAPA)) +
  geom_violin() +  
  facet_wrap(~measure, scales = "free_y") +
  theme_bw() +
  scale_y_continuous(name=NULL) + 
  scale_x_discrete(name=NULL) + 
  scale_colour_manual(values=colvalAPA12) +
  theme_cowplot(font_size = 8, line_size = 0.1) +
  theme(panel.grid.minor = element_blank(),
        axis.text.x = element_blank(),
        strip.text = element_text(face = "italic"),
        legend.position="none",
        axis.title = element_blank())  +
  scale_fill_manual(values=colorvalgenoAPA2)  
qpcrfigure
```

    ## Warning: Removed 5 rows containing non-finite values (stat_ydensity).

![](../results/newintegrativefigures-2.png)

``` r
pdf(file="../figures/4-qpcrfigure.pdf", width=3.5, height=2)
plot(qpcrfigure)
```

    ## Warning: Removed 5 rows containing non-finite values (stat_ydensity).

``` r
dev.off()
```

    ## quartz_off_screen 
    ##                 2

Statistics
----------

``` r
aov <- aov(Entrances ~ genoAPA, data=wtfmr1)
summary(aov) 
```

    ##              Df Sum Sq Mean Sq F value   Pr(>F)    
    ## genoAPA       3   6560  2186.8   30.08 3.35e-15 ***
    ## Residuals   146  10614    72.7                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
TukeyHSD(aov, which = "genoAPA") 
```

    ##   Tukey multiple comparisons of means
    ##     95% family-wise confidence level
    ## 
    ## Fit: aov(formula = Entrances ~ genoAPA, data = wtfmr1)
    ## 
    ## $genoAPA
    ##                                         diff        lwr        upr
    ## FMR1-KO_untrained-WT_untrained      6.450000   1.098074  11.801926
    ## WT_trained-WT_untrained           -10.986364 -15.591073  -6.381655
    ## FMR1-KO_trained-WT_untrained       -4.350000  -9.999479   1.299479
    ## WT_trained-FMR1-KO_untrained      -17.436364 -22.465799 -12.406928
    ## FMR1-KO_trained-FMR1-KO_untrained -10.800000 -16.800707  -4.799293
    ## FMR1-KO_trained-WT_trained          6.636364   1.291392  11.981335
    ##                                       p adj
    ## FMR1-KO_untrained-WT_untrained    0.0111613
    ## WT_trained-WT_untrained           0.0000000
    ## FMR1-KO_trained-WT_untrained      0.1923206
    ## WT_trained-FMR1-KO_untrained      0.0000000
    ## FMR1-KO_trained-FMR1-KO_untrained 0.0000386
    ## FMR1-KO_trained-WT_trained        0.0083242

``` r
aov <- aov(grim ~ genoAPA, data=all)
summary(aov) 
```

    ##             Df Sum Sq Mean Sq F value Pr(>F)
    ## genoAPA      3  289.2   96.40   1.115   0.36
    ## Residuals   27 2334.6   86.47

``` r
TukeyHSD(aov, which = "genoAPA") 
```

    ##   Tukey multiple comparisons of means
    ##     95% family-wise confidence level
    ## 
    ## Fit: aov(formula = grim ~ genoAPA, data = all)
    ## 
    ## $genoAPA
    ##                                         diff        lwr       upr
    ## WT_trained-WT_untrained            6.9861111  -4.628741 18.600964
    ## FMR1-KO_untrained-WT_untrained     2.3472222 -11.395657 16.090101
    ## FMR1-KO_trained-WT_untrained       6.8250000  -7.681946 21.331946
    ## FMR1-KO_untrained-WT_trained      -4.6388889 -17.362322  8.084545
    ## FMR1-KO_trained-WT_trained        -0.1611111 -13.706240 13.384018
    ## FMR1-KO_trained-FMR1-KO_untrained  4.4777778 -10.931065 19.886621
    ##                                       p adj
    ## WT_trained-WT_untrained           0.3709829
    ## FMR1-KO_untrained-WT_untrained    0.9655554
    ## FMR1-KO_trained-WT_untrained      0.5785269
    ## FMR1-KO_untrained-WT_trained      0.7519438
    ## FMR1-KO_trained-WT_trained        0.9999873
    ## FMR1-KO_trained-FMR1-KO_untrained 0.8559648

``` r
aov <- aov(prkcz ~ genoAPA, data=all)
summary(aov) 
```

    ##             Df Sum Sq Mean Sq F value Pr(>F)
    ## genoAPA      3   60.9   20.29   0.701   0.56
    ## Residuals   26  752.2   28.93               
    ## 1 observation deleted due to missingness

``` r
TukeyHSD(aov, which = "genoAPA") 
```

    ##   Tukey multiple comparisons of means
    ##     95% family-wise confidence level
    ## 
    ## Fit: aov(formula = prkcz ~ genoAPA, data = all)
    ## 
    ## $genoAPA
    ##                                        diff       lwr       upr     p adj
    ## WT_trained-WT_untrained           1.5873016 -5.430335  8.604938 0.9245835
    ## FMR1-KO_untrained-WT_untrained    2.3650794 -5.844118 10.574277 0.8581556
    ## FMR1-KO_trained-WT_untrained      4.4761905 -4.163741 13.116122 0.4979213
    ## FMR1-KO_untrained-WT_trained      0.7777778 -6.599968  8.155524 0.9913696
    ## FMR1-KO_trained-WT_trained        2.8888889 -4.965321 10.743099 0.7456112
    ## FMR1-KO_trained-FMR1-KO_untrained 2.1111111 -6.823783 11.046005 0.9152268

``` r
aov <- aov(rpl19 ~ genoAPA, data=all)
summary(aov) 
```

    ##             Df    Sum Sq  Mean Sq F value Pr(>F)  
    ## genoAPA      3 106435946 35478649    3.01 0.0475 *
    ## Residuals   27 318243207 11786785                 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
TukeyHSD(aov, which = "genoAPA") 
```

    ##   Tukey multiple comparisons of means
    ##     95% family-wise confidence level
    ## 
    ## Fit: aov(formula = rpl19 ~ genoAPA, data = all)
    ## 
    ## $genoAPA
    ##                                        diff         lwr       upr
    ## WT_trained-WT_untrained            265.0139 -4023.26120  4553.289
    ## FMR1-KO_untrained-WT_untrained    2146.7639 -2927.19162  7220.719
    ## FMR1-KO_trained-WT_untrained      5195.0417  -161.01220 10551.096
    ## FMR1-KO_untrained-WT_trained      1881.7500 -2815.82000  6579.320
    ## FMR1-KO_trained-WT_trained        4930.0278   -70.91737  9930.973
    ## FMR1-KO_trained-FMR1-KO_untrained 3048.2778 -2640.76201  8737.318
    ##                                       p adj
    ## WT_trained-WT_untrained           0.9982360
    ## FMR1-KO_untrained-WT_untrained    0.6577930
    ## FMR1-KO_trained-WT_untrained      0.0597402
    ## FMR1-KO_untrained-WT_trained      0.6948162
    ## FMR1-KO_trained-WT_trained        0.0544027
    ## FMR1-KO_trained-FMR1-KO_untrained 0.4709398

``` r
aov <- aov(IO_Max ~ genoAPA, data=all)
summary(aov) 
```

    ##             Df Sum Sq Mean Sq F value Pr(>F)
    ## genoAPA      3   5025    1675   1.541  0.231
    ## Residuals   23  24997    1087               
    ## 4 observations deleted due to missingness

``` r
TukeyHSD(aov, which = "genoAPA") 
```

    ##   Tukey multiple comparisons of means
    ##     95% family-wise confidence level
    ## 
    ## Fit: aov(formula = IO_Max ~ genoAPA, data = all)
    ## 
    ## $genoAPA
    ##                                         diff       lwr      upr     p adj
    ## WT_trained-WT_untrained           -34.285714 -80.26160 11.69017 0.1948227
    ## FMR1-KO_untrained-WT_untrained    -28.419048 -79.17505 22.33695 0.4258746
    ## FMR1-KO_trained-WT_untrained      -24.605714 -78.02486 28.81344 0.5875756
    ## FMR1-KO_untrained-WT_trained        5.866667 -42.21606 53.94940 0.9863976
    ## FMR1-KO_trained-WT_trained          9.680000 -41.20598 60.56598 0.9518093
    ## FMR1-KO_trained-FMR1-KO_untrained   3.813333 -51.42952 59.05619 0.9974554
