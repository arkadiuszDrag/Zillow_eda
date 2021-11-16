EDA Zillow
================
Arkadiusz Drąg
11/16/2021

Explolatory analysis of Zillow dataset. We are provided with a list of
real estate properties in three counties (Los Angeles, Orange and
Ventura, California) data in 2016.

Zillow provides a “Zestimate”, which is an estimated property value.

``` r
head(properties)
```

    ## # A tibble: 6 x 58
    ##   parcelid airconditioning… architecturalst… basementsqft bathroomcnt bedroomcnt
    ##      <dbl>            <dbl> <lgl>                   <dbl>       <dbl>      <dbl>
    ## 1 10754147               NA NA                         NA           0          0
    ## 2 10759547               NA NA                         NA           0          0
    ## 3 10843547               NA NA                         NA           0          0
    ## 4 10859147               NA NA                         NA           0          0
    ## 5 10879947               NA NA                         NA           0          0
    ## 6 10898347               NA NA                         NA           0          0
    ## # … with 52 more variables: buildingclasstypeid <dbl>,
    ## #   buildingqualitytypeid <dbl>, calculatedbathnbr <dbl>, decktypeid <dbl>,
    ## #   finishedfloor1squarefeet <dbl>, calculatedfinishedsquarefeet <dbl>,
    ## #   finishedsquarefeet12 <dbl>, finishedsquarefeet13 <dbl>,
    ## #   finishedsquarefeet15 <dbl>, finishedsquarefeet50 <dbl>,
    ## #   finishedsquarefeet6 <dbl>, fips <chr>, fireplacecnt <dbl>,
    ## #   fullbathcnt <dbl>, garagecarcnt <dbl>, garagetotalsqft <dbl>,
    ## #   hashottuborspa <lgl>, heatingorsystemtypeid <dbl>, latitude <dbl>,
    ## #   longitude <dbl>, lotsizesquarefeet <dbl>, poolcnt <dbl>, poolsizesum <dbl>,
    ## #   pooltypeid10 <lgl>, pooltypeid2 <lgl>, pooltypeid7 <dbl>,
    ## #   propertycountylandusecode <chr>, propertylandusetypeid <dbl>,
    ## #   propertyzoningdesc <chr>, rawcensustractandblock <chr>, regionidcity <dbl>,
    ## #   regionidcounty <dbl>, regionidneighborhood <dbl>, regionidzip <dbl>,
    ## #   roomcnt <dbl>, storytypeid <dbl>, threequarterbathnbr <dbl>,
    ## #   typeconstructiontypeid <lgl>, unitcnt <dbl>, yardbuildingsqft17 <dbl>,
    ## #   yardbuildingsqft26 <dbl>, yearbuilt <dbl>, numberofstories <dbl>,
    ## #   fireplaceflag <lgl>, structuretaxvaluedollarcnt <dbl>,
    ## #   taxvaluedollarcnt <dbl>, assessmentyear <dbl>, landtaxvaluedollarcnt <dbl>,
    ## #   taxamount <dbl>, taxdelinquencyflag <chr>, taxdelinquencyyear <dbl>,
    ## #   censustractandblock <dbl>

``` r
head(transactions)
```

    ## # A tibble: 6 x 3
    ##   parcelid logerror transactiondate
    ##      <dbl>    <dbl> <date>         
    ## 1 11016594   0.0276 2016-01-01     
    ## 2 14366692  -0.168  2016-01-01     
    ## 3 12098116  -0.004  2016-01-01     
    ## 4 12643413   0.0218 2016-01-02     
    ## 5 14432541  -0.005  2016-01-02     
    ## 6 11509835  -0.270  2016-01-02

``` r
head(sample_submission)
```

    ## # A tibble: 6 x 7
    ##   ParcelId `201610` `201611` `201612` `201710` `201711` `201712`
    ##      <dbl>    <dbl>    <dbl>    <dbl>    <dbl>    <dbl>    <dbl>
    ## 1 10754147        0        0        0        0        0        0
    ## 2 10759547        0        0        0        0        0        0
    ## 3 10843547        0        0        0        0        0        0
    ## 4 10859147        0        0        0        0        0        0
    ## 5 10879947        0        0        0        0        0        0
    ## 6 10898347        0        0        0        0        0        0

``` r
properties <- properties %>% rename(
  id_parcel = parcelid,
  build_year = yearbuilt,
  area_basement = basementsqft,
  area_patio = yardbuildingsqft17,
  area_shed = yardbuildingsqft26, 
  area_pool = poolsizesum,  
  area_lot = lotsizesquarefeet, 
  area_garage = garagetotalsqft,
  area_firstfloor_finished = finishedfloor1squarefeet,
  area_total_calc = calculatedfinishedsquarefeet,
  area_base = finishedsquarefeet6,
  area_live_finished = finishedsquarefeet12,
  area_liveperi_finished = finishedsquarefeet13,
  area_total_finished = finishedsquarefeet15,  
  area_unknown = finishedsquarefeet50,
  num_unit = unitcnt, 
  num_story = numberofstories,  
  num_room = roomcnt,
  num_bathroom = bathroomcnt,
  num_bedroom = bedroomcnt,
  num_bathroom_calc = calculatedbathnbr,
  num_bath = fullbathcnt,  
  num_75_bath = threequarterbathnbr, 
  num_fireplace = fireplacecnt,
  num_pool = poolcnt,  
  num_garage = garagecarcnt,  
  region_county = regionidcounty,
  region_city = regionidcity,
  region_zip = regionidzip,
  region_neighbor = regionidneighborhood,  
  tax_total = taxvaluedollarcnt,
  tax_building = structuretaxvaluedollarcnt,
  tax_land = landtaxvaluedollarcnt,
  tax_property = taxamount,
  tax_year = assessmentyear,
  tax_delinquency = taxdelinquencyflag,
  tax_delinquency_year = taxdelinquencyyear,
  zoning_property = propertyzoningdesc,
  zoning_landuse = propertylandusetypeid,
  zoning_landuse_county = propertycountylandusecode,
  flag_fireplace = fireplaceflag, 
  flag_tub = hashottuborspa,
  quality = buildingqualitytypeid,
  framing = buildingclasstypeid,
  material = typeconstructiontypeid,
  deck = decktypeid,
  story = storytypeid,
  heating = heatingorsystemtypeid,
  aircon = airconditioningtypeid,
  architectural_style= architecturalstyletypeid
)
```

``` r
transactions <- transactions %>% rename(
  id_parcel = parcelid,
  date = transactiondate
)
```

``` r
properties <- properties %>% 
  mutate(tax_delinquency = ifelse(tax_delinquency=="Y",1,0),
         flag_fireplace = ifelse(flag_fireplace=="Y",1,0),
         flag_tub = ifelse(flag_tub=="Y",1,0))
```

``` r
head(transactions["date"])
```

    ## # A tibble: 6 x 1
    ##   date      
    ##   <date>    
    ## 1 2016-01-01
    ## 2 2016-01-01
    ## 3 2016-01-01
    ## 4 2016-01-02
    ## 5 2016-01-02
    ## 6 2016-01-02

``` r
tmp <- transactions %>% mutate(year_month = make_date(year=year(date), month=month(date)))
tmp
```

    ## # A tibble: 90,275 x 4
    ##    id_parcel logerror date       year_month
    ##        <dbl>    <dbl> <date>     <date>    
    ##  1  11016594   0.0276 2016-01-01 2016-01-01
    ##  2  14366692  -0.168  2016-01-01 2016-01-01
    ##  3  12098116  -0.004  2016-01-01 2016-01-01
    ##  4  12643413   0.0218 2016-01-02 2016-01-01
    ##  5  14432541  -0.005  2016-01-02 2016-01-01
    ##  6  11509835  -0.270  2016-01-02 2016-01-01
    ##  7  12286022   0.044  2016-01-02 2016-01-01
    ##  8  17177301   0.164  2016-01-02 2016-01-01
    ##  9  14739064  -0.003  2016-01-02 2016-01-01
    ## 10  14677559   0.0843 2016-01-03 2016-01-01
    ## # … with 90,265 more rows

``` r
tmp %>%
  group_by(year_month) %>% count() %>%
  ggplot(aes(x=year_month, y=n)) +
  geom_bar(stat="identity",fill="red") +
  geom_vline(aes(xintercept=as.numeric(as.Date("2016-10-01"))), size=2)
```

![](Zillow_eda_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
transactions %>%
  ggplot(aes(x=logerror)) +
  geom_histogram(bins=400,fill="red") +
  theme_bw() + theme(axis.title = element_text(size=16), axis.text = element_text(size=14)) +
  ylab("Count") + coord_cartesian(x=c(-0.5,0.5))
```

![](Zillow_eda_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
transactions <- transactions %>% mutate(abs_logerror = abs(logerror))
transactions %>%
  ggplot(aes(x=abs_logerror)) +
  geom_histogram(bins=400, fill="red") +
  theme_bw() + theme(axis.title = element_text(size=16), axis.text = element_text(size=14)) +
  ylab("Count") + coord_cartesian(x=c(0,0.5))
```

![](Zillow_eda_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
transactions %>%
  mutate(year_month = make_date(year = year(date), month = month(date))) %>%
  group_by(year_month) %>% summarize(mean_abs_logerror = mean(abs_logerror)) %>%
  ggplot(aes(x=year_month, y=mean_abs_logerror)) +
  geom_line(size=1.5, color="red") + 
  geom_point(size=5, color="red") + theme_bw()
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

![](Zillow_eda_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
transactions %>%
  mutate(year_month = make_date(year = year(date), month = month(date))) %>%
  group_by(year_month) %>% summarize(mean_logerror = mean(logerror)) %>%
  ggplot(aes(x=year_month, y=mean_logerror)) +
  geom_line(size=1.5, color="red") +
  geom_point(size=5, color="red") + theme_bw()
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

![](Zillow_eda_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
missing_values <- properties %>% summarize_each(funs(sum(is.na(.))/n()))
```

    ## Warning: `summarise_each_()` is deprecated as of dplyr 0.7.0.
    ## Please use `across()` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_warnings()` to see where this warning was generated.

    ## Warning: `funs()` is deprecated as of dplyr 0.8.0.
    ## Please use a list of either functions or lambdas: 
    ## 
    ##   # Simple named list: 
    ##   list(mean = mean, median = median)
    ## 
    ##   # Auto named with `tibble::lst()`: 
    ##   tibble::lst(mean, median)
    ## 
    ##   # Using lambdas
    ##   list(~ mean(., trim = .2), ~ median(., na.rm = TRUE))
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_warnings()` to see where this warning was generated.

``` r
missing_values <- gather(missing_values, key="feature", value="missing_pct")
view(missing_values)
```

``` r
missing_values %>%
  ggplot(aes(x = reorder(feature,-missing_pct), y = missing_pct)) +
  geom_bar(stat = "identity", fill="red") +
  coord_flip() +theme_bw()
```

![](Zillow_eda_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
good_features <- filter(missing_values, missing_pct<0.75)

vars <- good_features$feature[str_detect(good_features$feature,'num_')]
```

``` r
cor_tmp <- transactions %>% left_join(properties, by="id_parcel")

tmp <- cor_tmp %>% select(one_of(c(vars, "abs_logerror")))

corrplot(cor(tmp, use="complete.obs"), type="lower")
```

![](Zillow_eda_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
vars <- good_features$feature[str_detect(good_features$feature, 'area_')]

tmp <- cor_tmp %>% select(one_of(c(vars, "abs_logerror")))

corrplot(cor(tmp, use="complete.obs"), type="lower")
```

![](Zillow_eda_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

``` r
vars <- setdiff(good_features$feature[str_detect(good_features$feature, "tax_")], c('tax_delinquency', 'tax_year'))

tmp <- cor_tmp %>% select(one_of(c(vars, "logerror")))

corrplot(cor(tmp, use='complete.obs'), type='lower')
```

![](Zillow_eda_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

``` r
cor_tmp %>%
  ggplot(aes(x=build_year)) + geom_line(stat="density", color="red", size=1.2) + theme_bw()
```

    ## Warning: Removed 756 rows containing non-finite values (stat_density).

![](Zillow_eda_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

``` r
cor_tmp %>%
  group_by(build_year) %>%
  summarize(mean_abs_logerror=mean(abs_logerror)) %>%
  ggplot(aes(x=build_year, y=mean_abs_logerror)) +
  geom_point(color='red') +
  geom_smooth(color='gray40') +
  coord_cartesian(ylim=c(0,0.25)) + theme_bw()
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 1 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](Zillow_eda_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

``` r
cor_tmp %>%
  group_by(build_year) %>%
  summarize(mean_logerror=mean(logerror)) %>%
  ggplot(aes(x=build_year, y=mean_logerror)) +
  geom_point(color='red') +
  geom_smooth(color='gray40') +
  coord_cartesian(ylim=c(0,0.075)) + theme_bw()
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 1 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 1 rows containing missing values (geom_point).

![](Zillow_eda_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

``` r
transactions <- transactions %>% mutate(percentile = cut(abs_logerror,quantile(abs_logerror,
                                                                               probs=c(0, 0.1, 0.25, 0.75, 0.9, 1)
                                                                               ,names = FALSE)
                                                         ,include.lowest = TRUE,labels=FALSE))
```

``` r
tmp1 <- transactions %>%
  filter(percentile == 1) %>%
  sample_n(5000) %>%
  left_join(properties, by='id_parcel')
```

``` r
tmp2 <- transactions %>% 
  filter(percentile == 5) %>% 
  sample_n(5000) %>% 
  left_join(properties, by="id_parcel")
```

``` r
tmp3 <- transactions %>% 
  filter(percentile == 3) %>% 
  sample_n(5000) %>% 
  left_join(properties, by="id_parcel")
```

``` r
tmp1 <- tmp1 %>% mutate(type="best_fit")
tmp2 <- tmp2 %>% mutate(type="worst_fit")
tmp3 <- tmp3 %>% mutate(type="typical_fit")
```

``` r
tmp <- bind_rows(tmp1, tmp2, tmp3)
tmp <- tmp %>% mutate(type = factor(type,levels = c("worst_fit", "typical_fit", "best_fit")))
```

``` r
col_pal <- "Set1"

tmp %>% ggplot(aes(x=latitude, fill=type, color=type)) +
  geom_line(stat="density", size=1.2) + theme_bw() + 
  scale_fill_brewer(palette=col_pal) +
  scale_color_brewer(palette=col_pal)
```

![](Zillow_eda_files/figure-gfm/unnamed-chunk-28-1.png)<!-- -->

``` r
tmptrans <- transactions %>% 
  left_join(properties, by="id_parcel")

tmptrans %>%
  ggplot(aes(x=latitude, y=abs_logerror)) +
  geom_smooth(color="red") + theme_bw()
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

![](Zillow_eda_files/figure-gfm/unnamed-chunk-29-1.png)<!-- -->

``` r
tmptrans <- tmptrans %>% mutate(overunder = ifelse(logerror<0, "under", "over"))

tmptrans %>%
  ggplot(aes(x=latitude, y=abs(logerror), fill=overunder, color=overunder)) +
  geom_smooth(fill="gray40") + theme_bw() +
  scale_fill_brewer(palette=col_pal) +
  scale_color_brewer(palette=col_pal)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

![](Zillow_eda_files/figure-gfm/unnamed-chunk-30-1.png)<!-- -->

``` r
tmptrans %>%
  ggplot(aes(x=longitude, y=abs(logerror), fill=overunder, color=overunder)) +
  geom_smooth(fill="gray40") + theme_bw() +
  scale_fill_brewer(palette=col_pal) +
  scale_color_brewer(palette=col_pal)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

![](Zillow_eda_files/figure-gfm/unnamed-chunk-31-1.png)<!-- -->

``` r
leaflet() %>%
  addTiles() %>%
  fitBounds(-118.5,33.8,-118.25,34.15) %>%
  addRectangles(-118.5,33.8,-118.25,34.15) %>%
  addMiniMap()
```

<div id="htmlwidget-f7edd21804c7e8e207de" style="width:672px;height:480px;" class="leaflet html-widget"></div>
<script type="application/json" data-for="htmlwidget-f7edd21804c7e8e207de">{"x":{"options":{"crs":{"crsClass":"L.CRS.EPSG3857","code":null,"proj4def":null,"projectedBounds":null,"options":{}}},"calls":[{"method":"addTiles","args":["//{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",null,null,{"minZoom":0,"maxZoom":18,"tileSize":256,"subdomains":"abc","errorTileUrl":"","tms":false,"noWrap":false,"zoomOffset":0,"zoomReverse":false,"opacity":1,"zIndex":1,"detectRetina":false,"attribution":"&copy; <a href=\"http://openstreetmap.org\">OpenStreetMap<\/a> contributors, <a href=\"http://creativecommons.org/licenses/by-sa/2.0/\">CC-BY-SA<\/a>"}]},{"method":"addRectangles","args":[33.8,-118.5,34.15,-118.25,null,null,{"interactive":true,"className":"","stroke":true,"color":"#03F","weight":5,"opacity":0.5,"fill":true,"fillColor":"#03F","fillOpacity":0.2,"smoothFactor":1,"noClip":false},null,null,null,{"interactive":false,"permanent":false,"direction":"auto","opacity":1,"offset":[0,0],"textsize":"10px","textOnly":false,"className":"","sticky":true},null]},{"method":"addMiniMap","args":[null,null,"bottomright",150,150,19,19,-5,false,false,false,false,false,false,{"color":"#ff7800","weight":1,"clickable":false},{"color":"#000000","weight":1,"clickable":false,"opacity":0,"fillOpacity":0},{"hideText":"Hide MiniMap","showText":"Show MiniMap"},[]]}],"fitBounds":[33.8,-118.5,34.15,-118.25,[]],"limits":{"lat":[33.8,34.15],"lng":[-118.5,-118.25]}},"evals":[],"jsHooks":[]}</script>
