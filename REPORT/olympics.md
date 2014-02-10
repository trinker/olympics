<link rel="stylesheet" href="./css/flip.css" />
<link rel="stylesheet" href="./css/popup_style.css" />
<link rel="stylesheet" href="./css/style.css" />
<script type="text/javascript" src="./js/jquery-1.9.1.min.js"></script>
<script type="text/javascript" src="./js/nhpup-ioshack_1.0.js"></script>
<script type="text/javascript" src="./js/nhpup_1.0.js"></script>
<script type="text/javascript" src="./js/nhpup_1.1.js"></script>
<script type="text/javascript" src="./js/reports.js"></script>




<div style="float:right;margin:-15px 20px 0px 20px;">
<img src="http://3.bp.blogspot.com/-ZoD0jyPPn2c/T5ASxK-hXLI/AAAAAAAACQQ/EArxD8xnUYw/s1600/olympic%2Bsymbol.jpg" width="140" height="70">
</div> For those who are addicted to R and haven't the time to click the mouse on a web browser you too can still be informed about the results of the [2014 Sochi Winter Olympics](http://www.sochi2014.com/).  I was inspired by [a SO response](http://stackoverflow.com/questions/11712353/downloading-live-olympic-medal-data-into-r) around the London games a couple years back.

## Packages to Load


```r
packs <- c("knitr", "ggplot2", "XML", "reshape2", "rCharts")
lapply(packs, require, character.only = T)
```

	
# The Script


```r
olympics <- 
function(theurl = "http://www.sochi2014.com/en/medal-standings", include.zero = FALSE) {

    invisible(lapply(c('ggplot2', 'XML', 'reshape2') , require, character.only=TRUE))

    ## Grab Data, Clean and Reshape
    raw <- readHTMLTable(theurl, header=FALSE, colClasses = c(rep("factor", 2), rep("numeric", 4)))
    raw <- as.data.frame(raw)[, -1]
    colnames(raw) <- c("Country", "Bronze", "Silver", "Gold", "Total")
    raw <- raw[order(raw[, "Total"]), ]
    if (!include.zero) {
        raw <- raw[raw[, "Total"] != 0, ]
    }
    raw[, "Country"] <- factor(raw[, "Country"], levels = raw[, "Country"])
	rownames(raw) <- NULL
    mdat <- melt(raw, value.name = "Count", variable.name = "Place", id.var = "Country")
    
    ## Plot the Data
    plot1 <- ggplot(mdat, aes(x = Count, y = Country, colour = Place)) +
      geom_point() +
      facet_grid(.~Place) + theme_bw()+
      scale_colour_manual(values=c("#CC6600", "#999999", "#FFCC33", "#000000")) 
    print(plot1)

    return(invisible(raw))
}
```



# The Visual Results


```r
x <- olympics()
```

![plot of chunk unnamed-chunk-3](http://dl.dropboxusercontent.com/u/61803503/wp/figure/unnamed-chunk-3.png) 


# As a Data Table


```r
dTable(x)$show('inline', include_assets = TRUE, cdn = TRUE)
```

<link rel='stylesheet' href=http://ajax.aspnetcdn.com/ajax/jquery.dataTables/1.9.4/css/jquery.dataTables.css>
<script type='text/javascript' src=http://code.jquery.com/jquery-1.10.2.min.js></script>
<script type='text/javascript' src=http://ajax.aspnetcdn.com/ajax/jquery.dataTables/1.9.4/jquery.dataTables.min.js></script> 
 <style>
  .rChart {
    display: block;
    margin-left: auto; 
    margin-right: auto;
    width: 800px;
    height: 400px;
  }  
  </style>
<table id = 'chart1cc46b1e5b56' class = 'rChart datatables'></table>
<script type="text/javascript" charset="utf-8">
  var chartParamschart1cc46b1e5b56 = {
 "dom": "chart1cc46b1e5b56",
"width":    800,
"height":    400,
"table": {
 "aaData": [
 [
 "Germany",
     1,
     0,
     0,
     1 
],
[
 "Poland",
     1,
     0,
     0,
     1 
],
[
 "Slovakia",
     1,
     0,
     0,
     1 
],
[
 "Switzerland",
     1,
     0,
     0,
     1 
],
[
 "Finland",
     0,
     1,
     0,
     1 
],
[
 "Slovenia",
     0,
     1,
     0,
     1 
],
[
 "Great Britain",
     0,
     0,
     1,
     1 
],
[
 "Ukraine",
     0,
     0,
     1,
     1 
],
[
 "Austria",
     1,
     1,
     0,
     2 
],
[
 "Sweden",
     0,
     2,
     0,
     2 
],
[
 "Czech Republic",
     0,
     1,
     1,
     2 
],
[
 "Italy",
     0,
     1,
     1,
     2 
],
[
 "Netherlands",
     2,
     1,
     1,
     4 
],
[
 "United States",
     2,
     0,
     2,
     4 
],
[
 "Canada",
     1,
     2,
     1,
     4 
],
[
 "Russian Fed.",
     1,
     2,
     1,
     4 
],
[
 "Norway",
     2,
     1,
     4,
     7 
] 
],
"aoColumns": [
 {
 "sTitle": "Country" 
},
{
 "sTitle": "Bronze" 
},
{
 "sTitle": "Silver" 
},
{
 "sTitle": "Gold" 
},
{
 "sTitle": "Total" 
} 
] 
},
"id": "chart1cc46b1e5b56" 
}
  $('#' + chartParamschart1cc46b1e5b56.id).removeClass("rChart")

  $(document).ready(function() {
		drawDataTable(chartParamschart1cc46b1e5b56)
	});
  function drawDataTable(chartParams){
    var dTable = $('#' + chartParams.dom).dataTable(
      chartParams.table
    );
    //first use rCharts width
  	$('#'+chartParams.id+"_wrapper").css("width",chartParams.width)  
		$('#'+chartParams.id+"_wrapper").css("width",chartParams.table.width)
    
    //then if specified change to table width
    $('#'+chartParams.id+"_wrapper").css("margin-left", "auto");
    $('#'+chartParams.id+"_wrapper").css("margin-right", "auto");
		dTable.fnAdjustColumnSizing();
  }
		
</script>

<br>

WordPress and Data Table don't play well together so you'll need to run the code to see it in action.

# Discussion

I have chosen a dot plot to display the data because it's easy to quickly get a sense of the data yet be able to compare relatively easily.  Dot plots take advantage of the powerful pre-attentive attribute of distance (The most powerful way to visually convey quantitative information).  Stephen Few gives his two cents about dot plots [here](http://www.perceptualedge.com/articles/b-eye/dot_plots.pdf).

I'm lazy but this would be a fun [Shiny app](http://www.rstudio.com/shiny/) to build.

Thanks to @Ramnath for [help implementing](https://github.com/yihui/knitr/issues/717#issuecomment-34590101) the chart as a [jQuery DataTable](http://www.datatables.net/).


<hr><em><font size="1">*Created using the <a href="https://github.com/trinker/reports" target="_blank">reports</a> package</font></em>
