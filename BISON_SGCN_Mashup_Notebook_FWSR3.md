BISON - SGCN Data Mashup Report for WSFR Region 3
Abby Benson  
April 3, 2018  


```r
# Data for this notebook was brought together and managed using the R script BISON_SGCN_Mashup_FWSRegion3.R
```

## General information about the data
The data contained in this report are a combination of the Species of Greatest Conservation Need (SGCN) as identified by the states in Fish
and Wildlife Service Wildlife and Sport Fish Restoration Program Region 3. SGCN lists were collated by USGS and matched to the Integrated 
Taxonomic Information System (ITIS), the World Register of Marine Species (WoRMS), Fish and Wildlife Service listing status information, 
and the NatureServe database. Species that were not found in ITIS or WoRMS are not considered part of the SGCN National List but are still 
included in this analysis.




### Number of Species of Greatest Conservation Need in Fish and Wildlife Service Region 3:


```r
nrow(FWSRegion3_list)
```

```
## [1] 1572
```


### Number of SGCN that are considered Endangered, Threatened, Candidate, or Under Review by the Fish and Wildlife Service:


```r
nrow(FWSRegion3_list[which(FWSRegion3_list$ListingStatus == "Candidate" | FWSRegion3_list$ListingStatus == "Endangered" | 
                             FWSRegion3_list$ListingStatus == "Threatened" | 
                             FWSRegion3_list$ListingStatus == "Under Review in the Candidate or Petition Process"),])
```

```
## [1] 78
```

```r
lsp1 <- ggplot(ls_r1, aes(x=count, y=class)) + geom_point(shape=1)

# Divide by levels of FWS listing status, in the vertical direction
lsp1 + facet_grid(ListingStatus ~ .)
```

![plot of chunk unnamed-chunk-3](https://github.com/albenson-usgs/BCB_DataWranglingAndAnalyses/blob/master/BISON-SGCN-FWSRegion3-FWSClassifications.png)


### BISON Occurrence Data Available for these SGCN grouped by the Class level of the taxonomic hierarchy
First we'll examine what the breakdown looks like for the types of observations for these species for all observations from all locations.
But as you can see, it's difficult to discern what's available for anything but birds


```r
op1 <- ggplot(o1_gather, aes(class, `Number of Occurrences` ))
op1 + geom_bar(aes(fill = type), position = position_stack(reverse = TRUE), stat = "identity") +
  coord_flip() +
  theme(legend.position = "top") 
```

![plot of chunk unnamed-chunk-4](https://github.com/albenson-usgs/BCB_DataWranglingAndAnalyses/blob/master/BISON-SGCN-FWSRegion3-BISONOccTypeWBirds.png)

Therefore we take birds out and see what is available for the rest of the classes.


```r
o1_1_gather <- o1_gather[which(o1_gather$class != "Aves (152)"),]
op2 <- ggplot(o1_1_gather, aes(class, `Number of Occurrences` ))
op2 + geom_bar(aes(fill = type), position = position_stack(reverse = TRUE), stat = "identity") +
  coord_flip() +
  theme(legend.position = "top") 
```

![plot of chunk unnamed-chunk-5](https://github.com/albenson-usgs/BCB_DataWranglingAndAnalyses/blob/master/BISON-SGCN-FWSRegion3-BISONOccTypeWOBirds.png)

Next we'll see how this breaks down for the region of interest- FWS WSFR Region 3


```r
p1 <- ggplot(r1, aes(x=class, y=Total)) + 
  geom_point(stat = "identity") +
  geom_point(col="tomato2", size=3) +   # Draw points
  geom_segment(aes(x=class, 
                   xend=class, 
                   y=min(Total), 
                   yend=max(Total)), 
               linetype="dashed", 
               size=0.1) +   # Draw dashed lines
  labs(title="BISON occurrences of SGCN", 
       subtitle="FWS WSFR Region 3", 
       caption="") +  
  theme(axis.title.x = element_blank()) +
  ylim(0,100000) +
  coord_flip()
p1 <- ggplotGrob(p1)
```

```
## Warning: Removed 2 rows containing missing values (geom_point).

## Warning: Removed 2 rows containing missing values (geom_point).
```

```
## Warning: Removed 22 rows containing missing values (geom_segment).
```

```r
p2 <- ggplot(r1, aes(x=class, y=Total)) + 
  geom_point(stat = "identity") +
  geom_point(col="tomato2", size=3) +   # Draw points
  geom_segment(aes(x=class, 
                   xend=class, 
                   y=min(Total), 
                   yend=max(Total)), 
               linetype="dashed", 
               size=0.1) + 
  labs(title="", 
       subtitle="", 
       caption="") + 
  theme(axis.title.y = element_blank(),
        axis.text.y=element_blank(),
        axis.ticks.y=element_blank(),
        axis.title.x = element_blank()) +
  ylim(100001,1000000) +
  coord_flip()
p2 <- ggplotGrob(p2)
```

```
## Warning: Removed 21 rows containing missing values (geom_point).
```

```
## Warning: Removed 21 rows containing missing values (geom_point).
```

```
## Warning: Removed 22 rows containing missing values (geom_segment).
```

```r
p3 <- ggplot(r1, aes(x=class, y=Total)) + 
  geom_point(stat = "identity") +
  geom_point(col="tomato2", size=3) +   # Draw points
  geom_segment(aes(x=class, 
                   xend=class, 
                   y=min(Total), 
                   yend=max(Total)), 
               linetype="dashed", 
               size=0.1) +
  labs(title="", 
       subtitle="", 
       caption="source: SGCN") + 
  theme(axis.title.y = element_blank(),
        axis.text.y=element_blank(),
        axis.ticks.y=element_blank(),
        axis.title.x = element_blank()) +
  ylim(1000001,61000000) + 
  coord_flip()
p3 <- ggplotGrob(p3)
```

```
## Warning: Removed 21 rows containing missing values (geom_point).
```

```
## Warning: Removed 21 rows containing missing values (geom_point).
```

```
## Warning: Removed 22 rows containing missing values (geom_segment).
```

```r
# grid.arrange(p1, p2, p3, ncol=3)
g <- cbind(p1, p2, p3, size = "first")
grid::grid.draw(g)
```

![plot of chunk unnamed-chunk-6](https://github.com/albenson-usgs/BCB_DataWranglingAndAnalyses/blob/master/BISON-SGCN-FWSRegion3-BISONOccDotPlot.png)


Stack bar chart showing the proportion of records that are in each state by class


```r
r1_gather <- gather(r1, key = "state", value = "occCount", -class)
r1_gather <- r1_gather[which(r1_gather$state != "classtotal" & r1_gather$state != "Total"),]
r1_1_gather <- r1_gather[which(r1_gather$class != "Aves (152)"),]
r1_2_gather <- r1_gather[which(r1_gather$class == "Aves (152)"),]
r1_1_gather$class <- factor(r1_1_gather$class, levels = r1_1_gather$class[order(r1$Total)])
```

```
## Warning in `levels<-`(`*tmp*`, value = if (nl == nL) as.character(labels)
## else paste0(labels, : duplicated levels in factors are deprecated
```

```r
g1 <- ggplot(r1_1_gather, aes(class, occCount))
g1 + geom_bar(aes(fill = state), position = position_stack(reverse = TRUE), stat = "identity") +
  coord_flip() +
  theme(legend.position = "top") + ylim(0,115000) #doesn't show Aves
```

```
## Warning in `levels<-`(`*tmp*`, value = if (nl == nL) as.character(labels)
## else paste0(labels, : duplicated levels in factors are deprecated

## Warning in `levels<-`(`*tmp*`, value = if (nl == nL) as.character(labels)
## else paste0(labels, : duplicated levels in factors are deprecated
```

![plot of chunk unnamed-chunk-7](https://github.com/albenson-usgs/BCB_DataWranglingAndAnalyses/blob/master/BISON-SGCN-FWSRegion3-StateOccStackedBar.png)

