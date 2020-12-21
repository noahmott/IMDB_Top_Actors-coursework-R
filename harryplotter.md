Top Actors
================
Noah Mott
12/15/2020

Run necessary libraries

``` r
library(tidyr)
```

    ## Warning: package 'tidyr' was built under R version 4.0.3

``` r
library(dplyr)
```

    ## Warning: package 'dplyr' was built under R version 4.0.3

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(ggplot2)
```

    ## Warning: package 'ggplot2' was built under R version 4.0.3

import Data

``` r
df<-read.csv("C:/Users/noahm/OneDrive/Desktop/IMDb Movie Titles Cleaned.csv")
```

clean data

``` r
country<-separate_rows(df, country, sep=",")
df1<-separate_rows(country, actors, sep=",")
df1$country<-trimws(df1$country)
df1$actors<-trimws(df1$actors)
#filter for us actors
df2<-df1 %>% filter(country=="USA")
```

``` r
#filter for number of votes
df2<-df2 %>% filter(votes>=10000)
#filter for time
df2<-df2 %>% filter(year>=1970)
```

``` r
#create count of films for each actor
df3<-count(df2, actors)
#filter new df for at least 25 films
df4<-subset(df3, df3$n>=25)
#create an average of average score for each actor
vec<-aggregate(df2$avg_vote, by=list(df2$actors), FUN=mean, na.rm=TRUE)
#merge actor averages and movie counts dataframes along actors
together<-merge(df4, vec, by.x=c("actors"), by.y=c("Group.1"))
#change columnnames
colnames(together)<-c("Actors", "Count_of_Films", "AVG_Rating")
#sort df
sorted<-together[order(-together$AVG_Rating),]
```

Plot Data

``` r
plotstuff<-rbind(head(sorted, 10), tail(sorted, 10))
plotstuff1<-plotstuff[order(-plotstuff$AVG_Rating),]
```

``` r
plot1<-ggplot(data=plotstuff1, aes(size=Count_of_Films, y=AVG_Rating, x=reorder(Actors,AVG_Rating)))+geom_hline(yintercept = mean(together$AVG_Rating), linetype="dashed", color="red", size=1)
plot1<-plot1+geom_point(shape=21, color="black", fill="green", stroke=2)+coord_flip()+annotate("text", x="Jean-Claude Van Damme", y=mean(together$AVG_Rating), label="Mean Actor Rating", size=4, angle=90, vjust=-0.6, hjust=-.1)
plot1<-plot1+labs(x="Actor", y="Average Movie Rating", size="Count of Films", title="Top & Bottom 10 US Actors by IMDb Average Film Rating Since 1970")
plot1<-plot1+theme(plot.background = element_rect(fill = "grey80", colour = "black"), panel.background=element_rect(fill="grey60"), legend.backgroun=element_rect(fill="grey80"), legend.key = element_rect(fill="grey80"))+theme(plot.title=element_text(hjust=.5))
plot1
```

![](harryplotter_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->
