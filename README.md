# osmtograph

![](https://github.com/ethicnology/osmtograph/blob/main/assets/osmtograph-polylines.png?raw=true)

## prerequisites

- [Osmium Tool](https://osmcode.org/osmium-tool/) or a filtered by highways
  OpenStreetMap file
- Bash Command Line (cat)

## installation

### download executable

Download the
[**lastest release**](https://github.com/ethicnology/osmtograph/releases) for
your system (**Linux, MacOS or Windows**)

### build from sources

clone this repository and then

```sh
cargo build --release
```

## procedure

#### export a map from [OpenStreetMap](https://www.openstreetmap.org)

![](https://github.com/ethicnology/osmtograph/blob/main/assets/example_animated?raw=true)

#### extract ways of type [highway](https://wiki.openstreetmap.org/wiki/Key:highway) from your map

```sh
osmium tags-filter map.osm w/highway -o highways-ways.osm
```

#### extract nodes

```sh
cat highways-ways.osm | ./osmtograph format | ./osmtograph nodes > nodes
```

#### extract links

```sh
cat highways-ways.osm | ./osmtograph format | ./osmtograph links > links
```

#### combine nodes and links into a graph

```sh
cat nodes links > raw-graph
```

#### remove two degree nodes, nodes with under delta links, links which length is under delta and return the largest connected components.
```sh
cat raw-graph | ./osmtograph heuristics --delta 5.0 > heuristics-graph
```

#### Discretize heuristics graph in equal parts which are between delta and delta*2
```sh
cat heuristics-graph | ./osmtograph discretize --delta 3.0 > discretized-graph
```

##### One line heuristics and discretization
```sh
cat raw-graph | ./osmtograph heuristics --delta 5.0 | ./osmtograph discretize --delta 3.0 > heuristics-discretized-graph
```

## output
### graph file
#### example
```sh
node_id␟latitude␟longitude #represents a node
node_id␟latitude␟longitude
node_id␟node_id #represents a link
node_id␟node_id
```

> **_NOTE:_** "␟" ASCII 31 (0x1F) Unit Separator - Used to indicate separation
> between units within a record. I choose this one because OSM data contains
> various special characters.

#### real life data
```sh
3758221295␟48.8275185␟2.3484976 #represents a node
3761637488␟48.8275416␟2.3486683
3761637488␟3758221295 #represents a link
```
#### heuristics graph delta=5.0
```sh
1829061610␟48.8260051␟2.3474783
21658501␟48.8279975␟2.3518307
1829061667␟48.8265177␟2.3501273
1829061607␟48.8278868␟2.347252
3761637486-3761637489␟48.827538␟2.348701
2576426850-2576426853-3761637482␟48.82743␟2.348672
92192237␟48.8275872␟2.3490245
1829061614␟48.8273732␟2.3487375
1829061602␟48.8275089␟2.3484223
2268836829␟48.8276001␟2.3486802
2268836829␟3761637486-3761637489
1829061614␟2576426850-2576426853-3761637482
3761637486-3761637489␟92192237
2576426850-2576426853-3761637482␟3761637486-3761637489
1829061602␟3761637486-3761637489
1829061614␟1829061667
1829061602␟1829061607
1829061602␟2576426850-2576426853-3761637482
1829061614␟92192237
1829061610␟1829061614
21658501␟92192237
```

#### discretized heuristics graph delta=5.0
```sh
1829061610-1829061614:15/35␟48.82659␟2.348018
1829061614-1829061667:1/27␟48.827343␟2.348789
21658501-92192237:8/41␟48.82792␟2.351283
21658501-92192237:32/41␟48.82768␟2.3496406
1829061610-1829061614:10/35␟48.826397␟2.3478382
21658501-92192237:15/41␟48.827847␟2.350804
1829061602-1829061607:8/19␟48.827667␟2.3479295
1829061602-1829061607:14/19␟48.82779␟2.34756
1829061602-1829061607:9/19␟48.827686␟2.347868
2576426850-2576426853-3761637482␟48.82743␟2.348672
1829061607␟48.8278868␟2.347252
21658501-92192237:1/41␟48.827988␟2.3517623
21658501-92192237:10/41␟48.8279␟2.3511462
21658501-92192237:31/41␟48.827686␟2.349709
21658501␟48.8279975␟2.3518307
1829061610-1829061614:20/35␟48.826786␟2.348198
1829061610-1829061614:21/35␟48.826824␟2.348234
1829061614-1829061667:23/27␟48.826645␟2.3499212
21658501-92192237:9/41␟48.827908␟2.3512146
1829061614-92192237:5/6␟48.827553␟2.3489766
1829061602-1829061607:4/19␟48.827587␟2.348176
1829061614-92192237:1/6␟48.827408␟2.3487854
21658501-92192237:19/41␟48.82781␟2.3505304
3761637486-3761637489-92192237:1/4␟48.82755␟2.3487818
1829061614-1829061667:22/27␟48.826675␟2.34987
1829061610-1829061614:23/35␟48.826904␟2.3483057
1829061614-1829061667:11/27␟48.827026␟2.3493037
1829061610-1829061614:3/35␟48.826122␟2.3475864
1829061610-1829061614:25/35␟48.82698␟2.3483777
1829061610-1829061614:34/35␟48.827335␟2.3487015
21658501-92192237:5/41␟48.82795␟2.3514886
21658501-92192237:40/41␟48.8276␟2.349093
21658501-92192237:4/41␟48.827957␟2.351557
1829061614-1829061667:19/27␟48.82677␟2.3497155
1829061602-1829061607:13/19␟48.827766␟2.3476214
21658501-92192237:38/41␟48.827618␟2.3492298
1829061614-1829061667:7/27␟48.827152␟2.3490977
1829061610-1829061614:13/35␟48.82651␟2.3479462
1829061602-3761637486-3761637489:3/4␟48.82753␟2.3486314
21658501-92192237:26/41␟48.82774␟2.3500512
1829061614-1829061667:18/27␟48.826805␟2.349664
1829061614-1829061667:16/27␟48.826866␟2.349561
21658501-92192237:11/41␟48.82789␟2.3510778
21658501-92192237:21/41␟48.82779␟2.3503933
1829061610-1829061614:32/35␟48.827255␟2.3486295
21658501-92192237:24/41␟48.82776␟2.350188
2576426850-2576426853-3761637482-3761637486-3761637489:1/2␟48.827484␟2.3486865
1829061614-1829061667:17/27␟48.826836␟2.3496125
1829061602-3761637486-3761637489:1/4␟48.827515␟2.348492
21658501-92192237:22/41␟48.827778␟2.3503249
1829061614-1829061667:3/27␟48.82728␟2.348892
1829061602-2576426850-2576426853-3761637482:1/4␟48.827488␟2.3484848
21658501-92192237:25/41␟48.827747␟2.3501196
3761637486-3761637489␟48.827538␟2.348701
21658501-92192237:13/41␟48.82787␟2.350941
1829061614-92192237:2/6␟48.827446␟2.348833
1829061610-1829061614:2/35␟48.826084␟2.3475504
21658501-92192237:2/41␟48.82798␟2.3516939
1829061602-1829061607:10/19␟48.82771␟2.3478062
21658501-92192237:18/41␟48.82782␟2.3505988
1829061610-1829061614:5/35␟48.8262␟2.3476582
1829061602-1829061607:2/19␟48.82755␟2.348299
1829061614-92192237:4/6␟48.827515␟2.348929
1829061610-1829061614:16/35␟48.82663␟2.348054
21658501-92192237:27/41␟48.82773␟2.3499827
21658501-92192237:34/41␟48.827656␟2.3495038
1829061614-1829061667:13/27␟48.82696␟2.3494067
1829061614-1829061667:15/27␟48.8269␟2.3495095
1829061614-1829061667:25/27␟48.826584␟2.3500242
1829061614-1829061667:20/27␟48.82674␟2.349767
21658501-92192237:16/41␟48.82784␟2.3507357
1829061610-1829061614:26/35␟48.827023␟2.3484137
21658501-92192237:28/41␟48.827717␟2.3499143
21658501-92192237:29/41␟48.82771␟2.349846
1829061614-1829061667:26/27␟48.82655␟2.3500757
1829061602-1829061607:3/19␟48.827568␟2.3482375
1829061602␟48.8275089␟2.3484223
1829061602-1829061607:5/19␟48.827606␟2.3481143
1829061602-1829061607:6/19␟48.82763␟2.3480527
1829061610-1829061614:19/35␟48.826748␟2.348162
1829061610-1829061614:27/35␟48.82706␟2.3484497
1829061614-1829061667:4/27␟48.827248␟2.3489435
1829061614-1829061667:6/27␟48.827183␟2.3490462
1829061614-1829061667:14/27␟48.82693␟2.349458
1829061614-1829061667:9/27␟48.827087␟2.3492007
1829061614-1829061667:5/27␟48.827217␟2.3489947
1829061614␟48.8273732␟2.3487375
1829061667␟48.8265177␟2.3501273
21658501-92192237:17/41␟48.827827␟2.3506672
1829061602-3761637486-3761637489:2/4␟48.827522␟2.3485618
1829061610-1829061614:18/35␟48.82671␟2.348126
21658501-92192237:30/41␟48.827698␟2.3497775
1829061610-1829061614:30/35␟48.82718␟2.3485577
1829061614-1829061667:10/27␟48.827057␟2.3492522
21658501-92192237:7/41␟48.82793␟2.3513515
21658501-92192237:6/41␟48.827938␟2.3514202
1829061610-1829061614:9/35␟48.826355␟2.3478022
21658501-92192237:35/41␟48.82765␟2.349435
1829061610-1829061614:24/35␟48.826942␟2.3483417
1829061610-1829061614:31/35␟48.827217␟2.3485935
21658501-92192237:3/41␟48.82797␟2.3516254
1829061610-1829061614:6/35␟48.82624␟2.3476942
1829061610-1829061614:12/35␟48.826473␟2.3479102
1829061610-1829061614:17/35␟48.826668␟2.34809
21658501-92192237:23/41␟48.827766␟2.3502564
1829061614-1829061667:2/27␟48.82731␟2.3488405
1829061610-1829061614:33/35␟48.827293␟2.3486655
1829061614-1829061667:21/27␟48.82671␟2.3498185
1829061610-1829061614:8/35␟48.826317␟2.3477662
21658501-92192237:12/41␟48.827877␟2.3510094
3761637486-3761637489-92192237:2/4␟48.82756␟2.3488626
1829061610␟48.8260051␟2.3474783
1829061602-1829061607:15/19␟48.82781␟2.3474982
1829061602-1829061607:16/19␟48.827827␟2.3474367
1829061614-1829061667:12/27␟48.826992␟2.3493552
1829061610-1829061614:4/35␟48.82616␟2.3476224
1829061602-1829061607:12/19␟48.827747␟2.3476832
1829061610-1829061614:1/35␟48.826042␟2.3475144
1829061614-1829061667:8/27␟48.82712␟2.3491492
1829061610-1829061614:7/35␟48.82628␟2.3477302
1829061610-1829061614:14/35␟48.826553␟2.347982
21658501-92192237:36/41␟48.827637␟2.3493667
1829061602-1829061607:18/19␟48.82787␟2.3473134
1829061602-2576426850-2576426853-3761637482:3/4␟48.82745␟2.3486094
1829061602-1829061607:1/19␟48.827526␟2.3483608
21658501-92192237:20/41␟48.827797␟2.350462
1829061610-1829061614:22/35␟48.826866␟2.3482697
2268836829␟48.8276001␟2.3486802
1829061602-1829061607:17/19␟48.827847␟2.3473752
1829061602-1829061607:7/19␟48.82765␟2.347991
1829061614-1829061667:24/27␟48.826614␟2.3499727
92192237␟48.8275872␟2.3490245
21658501-92192237:37/41␟48.82763␟2.3492982
1829061610-1829061614:29/35␟48.827137␟2.3485217
1829061614-92192237:3/6␟48.82748␟2.348881
1829061610-1829061614:11/35␟48.826435␟2.3478742
1829061610-1829061614:28/35␟48.8271␟2.3484857
21658501-92192237:14/41␟48.827858␟2.3508725
21658501-92192237:39/41␟48.827606␟2.3491614
21658501-92192237:33/41␟48.827667␟2.3495722
1829061602-1829061607:11/19␟48.82773␟2.3477447
1829061602-2576426850-2576426853-3761637482:2/4␟48.82747␟2.348547
3761637486-3761637489-92192237:3/4␟48.827576␟2.3489437
1829061614-1829061667:8/27␟1829061614-1829061667:9/27
1829061610-1829061614:10/35␟1829061610-1829061614:11/35
21658501-92192237:19/41␟21658501-92192237:20/41
21658501-92192237:5/41␟21658501-92192237:6/41
3761637486-3761637489-92192237:2/4␟3761637486-3761637489-92192237:3/4
1829061602-1829061607:4/19␟1829061602-1829061607:5/19
1829061602-1829061607:11/19␟1829061602-1829061607:12/19
1829061614-1829061667:3/27␟1829061614-1829061667:4/27
1829061610-1829061614:26/35␟1829061610-1829061614:27/35
2576426850-2576426853-3761637482-3761637486-3761637489:1/2␟3761637486-3761637489
1829061610-1829061614:15/35␟1829061610-1829061614:16/35
1829061610-1829061614:17/35␟1829061610-1829061614:18/35
1829061602-1829061607:16/19␟1829061602-1829061607:17/19
21658501-92192237:23/41␟21658501-92192237:24/41
1829061602-1829061607:14/19␟1829061602-1829061607:15/19
1829061602␟1829061602-3761637486-3761637489:1/4
1829061610-1829061614:3/35␟1829061610-1829061614:4/35
21658501-92192237:29/41␟21658501-92192237:30/41
1829061610-1829061614:7/35␟1829061610-1829061614:8/35
1829061610-1829061614:20/35␟1829061610-1829061614:21/35
1829061614-1829061667:15/27␟1829061614-1829061667:16/27
1829061614-1829061667:25/27␟1829061614-1829061667:26/27
1829061610-1829061614:28/35␟1829061610-1829061614:29/35
1829061602-1829061607:7/19␟1829061602-1829061607:8/19
1829061602-1829061607:9/19␟1829061602-1829061607:10/19
1829061610-1829061614:29/35␟1829061610-1829061614:30/35
1829061614-1829061667:4/27␟1829061614-1829061667:5/27
1829061614-1829061667:20/27␟1829061614-1829061667:21/27
21658501-92192237:28/41␟21658501-92192237:29/41
1829061614-1829061667:13/27␟1829061614-1829061667:14/27
1829061610-1829061614:12/35␟1829061610-1829061614:13/35
1829061610-1829061614:33/35␟1829061610-1829061614:34/35
1829061610-1829061614:4/35␟1829061610-1829061614:5/35
1829061614-1829061667:9/27␟1829061614-1829061667:10/27
1829061610-1829061614:16/35␟1829061610-1829061614:17/35
1829061610-1829061614:2/35␟1829061610-1829061614:3/35
1829061614-1829061667:26/27␟1829061667
21658501-92192237:1/41␟21658501-92192237:2/41
21658501-92192237:8/41␟21658501-92192237:9/41
21658501-92192237:22/41␟21658501-92192237:23/41
1829061602-1829061607:15/19␟1829061602-1829061607:16/19
21658501-92192237:27/41␟21658501-92192237:28/41
1829061610-1829061614:18/35␟1829061610-1829061614:19/35
1829061614-1829061667:5/27␟1829061614-1829061667:6/27
1829061610␟1829061610-1829061614:1/35
1829061614-92192237:1/6␟1829061614-92192237:2/6
1829061614-1829061667:10/27␟1829061614-1829061667:11/27
1829061610-1829061614:5/35␟1829061610-1829061614:6/35
1829061614-1829061667:16/27␟1829061614-1829061667:17/27
21658501-92192237:13/41␟21658501-92192237:14/41
21658501-92192237:37/41␟21658501-92192237:38/41
1829061610-1829061614:34/35␟1829061614
21658501-92192237:12/41␟21658501-92192237:13/41
1829061610-1829061614:1/35␟1829061610-1829061614:2/35
1829061610-1829061614:13/35␟1829061610-1829061614:14/35
1829061610-1829061614:22/35␟1829061610-1829061614:23/35
21658501-92192237:40/41␟92192237
1829061614-1829061667:24/27␟1829061614-1829061667:25/27
2576426850-2576426853-3761637482␟2576426850-2576426853-3761637482-3761637486-3761637489:1/2
1829061610-1829061614:23/35␟1829061610-1829061614:24/35
1829061614-1829061667:14/27␟1829061614-1829061667:15/27
21658501-92192237:33/41␟21658501-92192237:34/41
21658501-92192237:15/41␟21658501-92192237:16/41
1829061614-92192237:4/6␟1829061614-92192237:5/6
21658501-92192237:30/41␟21658501-92192237:31/41
1829061610-1829061614:14/35␟1829061610-1829061614:15/35
21658501-92192237:21/41␟21658501-92192237:22/41
1829061610-1829061614:32/35␟1829061610-1829061614:33/35
21658501-92192237:18/41␟21658501-92192237:19/41
1829061614-1829061667:7/27␟1829061614-1829061667:8/27
1829061614-1829061667:21/27␟1829061614-1829061667:22/27
3761637486-3761637489-92192237:1/4␟3761637486-3761637489-92192237:2/4
21658501-92192237:3/41␟21658501-92192237:4/41
1829061610-1829061614:8/35␟1829061610-1829061614:9/35
21658501-92192237:35/41␟21658501-92192237:36/41
1829061614-1829061667:1/27␟1829061614-1829061667:2/27
1829061614-92192237:3/6␟1829061614-92192237:4/6
1829061614-1829061667:6/27␟1829061614-1829061667:7/27
1829061614-1829061667:19/27␟1829061614-1829061667:20/27
21658501-92192237:24/41␟21658501-92192237:25/41
1829061602-1829061607:18/19␟1829061607
21658501-92192237:25/41␟21658501-92192237:26/41
1829061610-1829061614:25/35␟1829061610-1829061614:26/35
1829061614-1829061667:17/27␟1829061614-1829061667:18/27
1829061610-1829061614:31/35␟1829061610-1829061614:32/35
1829061614-1829061667:18/27␟1829061614-1829061667:19/27
2268836829␟3761637486-3761637489
1829061614␟1829061614-1829061667:1/27
1829061614-1829061667:23/27␟1829061614-1829061667:24/27
21658501-92192237:9/41␟21658501-92192237:10/41
1829061614-1829061667:22/27␟1829061614-1829061667:23/27
21658501-92192237:16/41␟21658501-92192237:17/41
21658501-92192237:26/41␟21658501-92192237:27/41
1829061602␟1829061602-2576426850-2576426853-3761637482:1/4
21658501-92192237:17/41␟21658501-92192237:18/41
1829061602-2576426850-2576426853-3761637482:3/4␟2576426850-2576426853-3761637482
21658501-92192237:20/41␟21658501-92192237:21/41
1829061614-1829061667:12/27␟1829061614-1829061667:13/27
21658501␟21658501-92192237:1/41
1829061602-1829061607:8/19␟1829061602-1829061607:9/19
21658501-92192237:31/41␟21658501-92192237:32/41
1829061602-2576426850-2576426853-3761637482:2/4␟1829061602-2576426850-2576426853-3761637482:3/4
21658501-92192237:38/41␟21658501-92192237:39/41
1829061610-1829061614:11/35␟1829061610-1829061614:12/35
1829061614␟1829061614-92192237:1/6
21658501-92192237:10/41␟21658501-92192237:11/41
1829061602-1829061607:13/19␟1829061602-1829061607:14/19
21658501-92192237:14/41␟21658501-92192237:15/41
1829061602-1829061607:10/19␟1829061602-1829061607:11/19
1829061610-1829061614:6/35␟1829061610-1829061614:7/35
21658501-92192237:39/41␟21658501-92192237:40/41
21658501-92192237:34/41␟21658501-92192237:35/41
1829061602-1829061607:17/19␟1829061602-1829061607:18/19
1829061614-92192237:5/6␟92192237
1829061610-1829061614:19/35␟1829061610-1829061614:20/35
1829061602-3761637486-3761637489:3/4␟3761637486-3761637489
1829061614-92192237:2/6␟1829061614-92192237:3/6
21658501-92192237:7/41␟21658501-92192237:8/41
1829061610-1829061614:30/35␟1829061610-1829061614:31/35
1829061602-3761637486-3761637489:2/4␟1829061602-3761637486-3761637489:3/4
21658501-92192237:4/41␟21658501-92192237:5/41
21658501-92192237:32/41␟21658501-92192237:33/41
3761637486-3761637489-92192237:3/4␟92192237
1829061610-1829061614:9/35␟1829061610-1829061614:10/35
1829061602-1829061607:6/19␟1829061602-1829061607:7/19
1829061602-1829061607:12/19␟1829061602-1829061607:13/19
1829061614␟2576426850-2576426853-3761637482
1829061602-3761637486-3761637489:1/4␟1829061602-3761637486-3761637489:2/4
1829061602-1829061607:2/19␟1829061602-1829061607:3/19
1829061614-1829061667:11/27␟1829061614-1829061667:12/27
1829061610-1829061614:24/35␟1829061610-1829061614:25/35
1829061610-1829061614:27/35␟1829061610-1829061614:28/35
1829061614-1829061667:2/27␟1829061614-1829061667:3/27
21658501-92192237:6/41␟21658501-92192237:7/41
21658501-92192237:36/41␟21658501-92192237:37/41
3761637486-3761637489␟3761637486-3761637489-92192237:1/4
1829061602␟1829061602-1829061607:1/19
1829061602-1829061607:1/19␟1829061602-1829061607:2/19
1829061610-1829061614:21/35␟1829061610-1829061614:22/35
1829061602-1829061607:3/19␟1829061602-1829061607:4/19
1829061602-2576426850-2576426853-3761637482:1/4␟1829061602-2576426850-2576426853-3761637482:2/4
21658501-92192237:2/41␟21658501-92192237:3/41
1829061602-1829061607:5/19␟1829061602-1829061607:6/19
21658501-92192237:11/41␟21658501-92192237:12/41
```