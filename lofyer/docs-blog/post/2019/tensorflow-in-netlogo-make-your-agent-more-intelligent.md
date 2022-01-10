---
title: "TensorFlow in NetLogo, Make Your Agent More Intelligent"
date: "2019-09-22"
categories: 
  - "abm"
  - "ai"
---

# 0\. Background

NetLogo is a very useful tools for ABM, and Python is also a handful language for building proof of concept.

In this post I will show you how to call python language in NetLogo. For more information please [follow here](https://github.com/NetLogo/Python-Extension).

# 1\. NetLogo version

[![](images/WX20190922-211123@2x-1024x958.png)](https://blog.lofyer.org/wp-content/uploads/WX20190922-211123@2x.png)

breed \[data-points data-point\]
breed \[centroids centroid\]

globals \[
  any-centroids-moved?
\]

to setup
  clear-all
  set-default-shape data-points "circle"
  set-default-shape centroids "x"
  generate-clusters
  reset-centroids
end

to generate-clusters
  let cluster-std-dev 20 - num-clusters
  let cluster-size num-data-points / num-clusters
  repeat num-clusters \[
    let center-x random-xcor / 1.5
    let center-y random-ycor / 1.5
    create-data-points cluster-size \[
      setxy center-x center-y
      set heading random 360
      fd abs random-normal 0 (cluster-std-dev / 2) ;; Divide by two because abs doubles the width
    \]
  \]
end

to reset-centroids
  set any-centroids-moved? true
  ask data-points \[ set color grey \]

  let colors base-colors
  ask centroids \[die\]
  create-centroids num-centroids \[
    move-to one-of data-points
    set size 5
    set color last colors + 1
    set colors butlast colors
  \]
  clear-all-plots
  reset-ticks
end

to go
  if not any-centroids-moved? \[stop\]
  set any-centroids-moved? false
  assign-clusters
  update-clusters
  tick
end

to assign-clusters
  ask data-points \[set color \[color\] of closest-centroid - 2\]
end

to update-clusters
  let movement-threshold 0.1
  ask centroids \[
    let my-points data-points with \[ shade-of? color \[ color \] of myself \]
    if any? my-points \[
      let new-xcor mean \[ xcor \] of my-points
      let new-ycor mean \[ ycor \] of my-points
      if distancexy new-xcor new-ycor > movement-threshold \[
        set any-centroids-moved? true
      \]
      setxy new-xcor new-ycor
    \]
  \]
  update-plots
end

to-report closest-centroid
  report min-one-of centroids \[ distance myself \]
end

to-report square-deviation
  report sum \[ (distance myself) ^ 2 \] of data-points with \[ closest-centroid = myself \]
end

; Copyright 2014 Uri Wilensky.
; See Info tab for full copyright and license.

 

# 2\. TensorFlow version

TensorFlow version: 1.14

import numpy as np
import tensorflow as tf

num\_points = 100
dimensions = 2
points = np.random.uniform(0, 1000, \[num\_points, dimensions\])

def input\_fn():
  return tf.compat.v1.train.limit\_epochs(
      tf.convert\_to\_tensor(points, dtype=tf.float32), num\_epochs=1)

num\_clusters = 5
kmeans = tf.contrib.factorization.KMeansClustering(
    num\_clusters=num\_clusters, use\_mini\_batch=False)

# train
num\_iterations = 10
previous\_centers = None
for \_ in xrange(num\_iterations):
  kmeans.train(input\_fn)
  cluster\_centers = kmeans.cluster\_centers()
  if previous\_centers is not None:
    print 'delta:', cluster\_centers - previous\_centers
  previous\_centers = cluster\_centers
  print 'score:', kmeans.score(input\_fn)
print 'cluster centers:', cluster\_centers

# map the input points to their clusters
cluster\_indices = list(kmeans.predict\_cluster\_index(input\_fn))
for i, point in enumerate(points):
  cluster\_index = cluster\_indices\[i\]
  center = cluster\_centers\[cluster\_index\]
  print 'point:', point, 'is in cluster', cluster\_index, 'centered at', center

 

# 3\. NetLogo with Python Extension version

Here's the snapshot.

[![](images/WX20191028-204937@2x.png)](https://blog.lofyer.org/wp-content/uploads/WX20191028-204937@2x.png)

And here's the code.

extensions \[ py \]

breed \[data-points data-point\]
breed \[centroids centroid\]

data-points-own \[
  cluster-id
\]

centroids-own \[
  cluster-id
  centx
  centy
\]

globals \[
  testoutput
  centroid-list
\]

to setup
  clear-all
  py:setup py:python
  (py:run
    "import tensorflow as tf"
    "import numpy as np"
  )
  set testoutput py:runresult "1"
  py:set "testoutput" testoutput
  set-default-shape data-points "circle"
  set-default-shape centroids "x"
  generate-clusters
  ; For python
  py:set "num\_points" num-clusters
  py:set "points" \[list xcor ycor\] of data-points
  py:set "num\_clusters" num-clusters
  py:set "num\_round" num-round
  if debug = True
  \[
    py:run "print('Points Cordinates:', points)" ;for debug
  \]
  ;reset-centroids
end

to generate-clusters
  set testoutput py:runresult "testoutput + 1"
  let cluster-std-dev cluster-range
  let cluster-size num-data-points / num-clusters
  repeat num-clusters \[
    let center-x random-xcor / 1.5
    let center-y random-ycor / 1.5
    create-data-points cluster-size \[
      setxy center-x center-y
      set heading random 360
      fd abs random-normal 0 (cluster-std-dev / 2)
    \]
  \]
end

to train
  ; Cluster center
  (py:run
    "points = np.asarray(points)"
    "def input\_fn():"
    "    return tf.compat.v1.train.limit\_epochs(tf.convert\_to\_tensor(points, dtype=tf.float32), num\_epochs=1)"
    "kmeans = tf.contrib.factorization.KMeansClustering(num\_clusters=num\_clusters, use\_mini\_batch=False)"
    "num\_iterations = num\_round"
    "previous\_centers = None"
    "for \_ in range(num\_iterations):"
    "    kmeans.train(input\_fn)"
    "    cluster\_centers = kmeans.cluster\_centers()"
    "    if previous\_centers is not None:"
    "        print(('delta:', cluster\_centers - previous\_centers))"
    "    previous\_centers = cluster\_centers"
    "    print(('score:', kmeans.score(input\_fn)))"
    "print(('cluster centers:', cluster\_centers))"
    "# map the input points to their clusters"
    "cluster\_indices = list(kmeans.predict\_cluster\_index(input\_fn))"
    "print('cluster indices: ', cluster\_indices)"
    "for i, point in enumerate(points):"
    "    cluster\_index = cluster\_indices\[i\]"
    "    center = cluster\_centers\[cluster\_index\]"
    "    print(('point:', point, 'is in cluster', cluster\_index, 'centered at', center))"
  )
end

to show-shape
  set centroid-list py:runresult "cluster\_centers"
  foreach centroid-list \[
    x -> create-centroids 1 \[
      set xcor ( item 0 x )
      set ycor ( item 1 x )
      set size 3
      set color white
    \]
  \]
end

 

Ref:

\[1\] [https://www.altoros.com/blog/using-k-means-clustering-in-tensorflow/](https://www.altoros.com/blog/using-k-means-clustering-in-tensorflow/)

\[2\] [https://www.tensorflow.org/api\_docs/python/tf/contrib/factorization/KMeansClustering](https://www.tensorflow.org/api_docs/python/tf/contrib/factorization/KMeansClustering)
