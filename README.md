Cytoscape Network Charts
================

A HTMLWidget wrapper for the [Cytoscape.js](http://js.cytoscape.org/) graph / network visualisation and analysis library.

A Minimal Example
-----------------

``` r
nodes <- data.frame(id = c('a','b'))
edges <- data.frame(id = 'ab', source = 'a', target = 'b')

cytoscape(nodes = nodes, edges = edges) 
```

![](README_files/figure-markdown_github/min-1.png)

Styling Edges and Nodes
-----------------------

``` r
ct <- cytoscape(nodes = nodes, edges = edges)

ct %>% node_style('background-color' = '#ff0000')
```

![](README_files/figure-markdown_github/style1-1.png)

``` r
ct %>% edge_style('line-color' = '#ff0000')
```

![](README_files/figure-markdown_github/style2-1.png)

With Real Data
--------------

Comtrade waste plastic exports for December 2017.

``` r
df <- cytoscape::comtrade

nodes <- data.frame(id = unique(c(df$reporter, df$partner)), stringsAsFactors = FALSE)
edges <- df %>%
    dplyr::select(source = reporter,
                  target = partner) %>%
    dplyr::mutate(id = paste(source, '_', target))
```

    ## Warning: package 'bindrcpp' was built under R version 3.4.4

``` r
cytoscape(nodes = nodes, edges = edges) %>% 
  layout('grid', rows = 4)
```

![](README_files/figure-markdown_github/plastics-1.png)

``` r
cytoscape(nodes = nodes, edges = edges) %>% 
  layout('breadthfirst', directed = TRUE)
```

![](README_files/figure-markdown_github/plastics-2.png)

With Geographic Location
------------------------

``` r
# n_loc <- nodes %>%
#   dplyr::do(cbind(., ggmap::geocode(.$id)))

coords <- cytoscape::coords %>%
  rename(x = lon,
         y = lat) %>%
  filter(id != 'World') %>%
  mutate_at('y', funs(-.))
edges <- filter(edges, 
                source %in% coords$id,
                target %in% coords$id)

cytoscape(nodes = coords, edges = edges) %>% 
  layout('preset') %>%
  node_style(width = 3, height = 3, 'font-size' = 8) %>%
  edge_style(width = 1)
```

![](README_files/figure-markdown_github/location-1.png)

Image Node Example
------------------

Replicate the `Cytograph.js` [images example](http://js.cytoscape.org/demos/images-breadthfirst-layout/). This example does not currently include the touch effects.

``` r
img_nodes <- data.frame(id = c('cat','bird','ladybug','aphid','rose','grasshopper','plant','wheat'),
                        images = c('https://farm2.staticflickr.com/1261/1413379559_412a540d29_b.jpg',
                                   'https://farm8.staticflickr.com/7272/7633179468_3e19e45a0c_b.jpg',
                                   'https://farm4.staticflickr.com/3063/2751740612_af11fb090b_b.jpg',
                                   'https://farm9.staticflickr.com/8316/8003798443_32d01257c8_b.jpg',
                                   'https://farm6.staticflickr.com/5109/5817854163_eaccd688f5_b.jpg',
                                   'https://farm7.staticflickr.com/6098/6224655456_f4c3c98589_b.jpg',
                                   'https://farm1.staticflickr.com/231/524893064_f49a4d1d10_z.jpg',
                                   'https://farm3.staticflickr.com/2660/3715569167_7e978e8319_b.jpg'),
                        stringsAsFactors = FALSE)
img_edges <- data.frame(source = c('cat','bird','bird','grasshopper','grasshopper','ladybug','aphid'),
                        target = c('bird','ladybug','grasshopper','plant','wheat','aphid','rose'),
                        stringsAsFactors = FALSE)

cytoscape(nodes = img_nodes,
          edges = img_edges) %>%
  layout(name = 'breadthfirst',
         directed = TRUE,
         padding = 10) %>%
  node_style('height' = 80,
             'width' = 80,
             'background-fit' = 'cover',
             'border-color' = '#000',
             'border-width' = 3,
             'border-opacity' = 0.5,
             'label' = NULL) %>%
  edge_style('curve-style' = 'bezier',
             'width' = 6,
             'target-arrow-shape' = 'triangle',
             'line-color' = '#ffaaaa',
             'target-arrow-color' = '#ffaaaa') %>%
  node_images()
```

![](README_files/figure-markdown_github/image-1.png)
