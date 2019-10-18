<p><em>Are you a company or data scientist that would like to <a href="http://www.zipfianacademy.com/getinvolved" target="_blank">get involved</a>? Give us a shout at <a href="mailto:hello@zipfianacademy.com" target="_blank">hello@zipfianacademy.com</a>.</em></p>

<p><em>If this post excites you, I encourage you to <a href="http://zipfiancollective.wufoo.com/forms/m7x3z9/" target="_blank">apply</a> to our 12 week <a href="http://zipfianacademy.com" target="_blank">immersive bootcamp</a> (applications close August 5th) where you will learn data science through hands-on exercises and real world projects!</em></p>

<p>In our previous <a href="http://blog.zipfianacademy.com/post/46864003608/a-practical-intro-to-data-science" target="_blank">post</a>, we outlined the best data science resources we have found online.  In this post, I will walk you through my general data science <a href="http://zipfianacademy.com/data/data-science-process.png" target="_blank">process</a> by analyzing the inspections of San Francisco restaurants using publicly available <a href="http://www.sfdph.org/dph/EH/Food/score/default.asp" target="_blank">data</a> from the Department of Public health.  I will explore this data to map the cleanliness of the city, and get a better perspective on the relative meaning of these scores by looking at statistics of the data.   Throughout the analysis, I show how to use a spectrum of powerful tools for data science (from UNIX shell to <a href="http://pandas.pydata.org/" target="_blank">pandas</a> and <a href="http://matplotlib.org/" target="_blank">matplotlib</a>) and provide some tips and data tricks.</p>

<p>All of the code is contained in an IPython notebook and can be <a href="http://nbviewer.ipython.org/urls/raw.github.com/Jay-Oh-eN/happy-healthy-hungry/master/h3.ipynb" target="_blank">viewed</a> or <a href="https://github.com/Jay-Oh-eN/happy-healthy-hungry" target="_blank">downloaded</a> (and run).</p>

<h3>tl;dr</h3>

<p>The mode of restaurants is perfect score of 100, the distribution is heavily skewed towards high values (mean of 92, 75% quartile of 98, 25% quartile of 88), and there exists a <a href="http://" target="_blank">long tail</a> of restaurants.  What does this mean, the most common score is 100, 75% of restaurants receive a score of 88 or better (and the scale is very nonlinear), i.e. you probably don’t want to eat at a restaurant that scored below 90.</p>

<p><a href="http://zipfianacademy.com/maps/h3/" target="_blank"><img src="http://media.tumblr.com/8f0e43ab4233e7fe04db9360f79df6b9/tumblr_inline_mqv2h3pU7e1qz4rgp.png" alt=""/></a></p>

<iframe src="http://zipfianacademy.com/maps/h3/"></iframe>

<p>Each restaurant is geographically binned using the D3.js <a href="https://github.com/d3/d3-plugins/tree/master/hexbin" target="_blank">hexbin</a> plugin.  The color gradient of each hexagon reflects the median inspection score of the bin, and the radius of the hexagon is proportional to the number of restaurants that fall in that the bin.  Binning is first computed with a uniform hexagon radius over the map, and then the radius of each individual hexagon is adjusted for how many restaurants ended up in its bin.</p>

<p>Large blue hexagons represent many high scoring restaurants and small red mean a few very poorly scoring restaurants.  The controls on the map allow users to adjust the radius (<strong>Bin:</strong>) of the hexagon for computing the binning as well as the range (<strong>Score:</strong>) of scores to show/use on the map.  The color of the <strong>Bin:</strong> slider represents the median color of the <strong>Score:</strong> range and its size represents the radius of the hexagons.  The colors of each of the <strong>Score:</strong> sliders represent the threshold color for that score, i.e. if the range is 40 - 100 the left slider’s color corresponds to a score of 40 and the right slider to a score of 100.  The colors for every score in-between are computed using a linear gradient.</p>

<h3>Motivation</h3>

<p>Somewhat recently, <a href="http://www.yelp.com/" target="_blank">Yelp</a> <a href="http://officialblog.yelp.com/2013/01/introducing-lives.html" target="_blank">announced</a> that it is <a href="http://foodinspectiondata.us/" target="_blank">partnering</a> with <a href="http://codeforamerica.org/" target="_blank">Code for America</a> and the <a href="https://data.sfgov.org/" target="_blank">City of San Francisco</a> to develop <a href="http://www.yelp.com/healthscores" target="_blank">LIVES</a>, an open data standard which allows municipalities to publish restaurant inspection data in a standardized format.  This is a step towards allows a much much more transparent government, leading ultimately to a more engaged citizenry.</p>

<p>To understand what those opaque numbers in restaurant windows mean, I set out to use statistics and data science to better grasp the implications of the ratings.</p>

<h3>Process</h3>

<p>The entire process has been documented in an IPython notebook <a href="http://nbviewer.ipython.org/urls/raw.github.com/Jay-Oh-eN/happy-healthy-hungry/master/h3.ipynb" target="_blank">here</a> and I hope anyone who is curious will run the code and review the analyses before they take the results at face value (because <a href="http://petewarden.com/2013/07/18/why-you-should-never-trust-a-data-scientist/" target="_blank">No one should trust a data scientist</a>).</p>

<p>Some interesting results and insights I have found can be summed up by the plots below.</p>

<p>In order to learn more about the relative rating of each restaurant and find out just how good a 90 is, I simply plotted all the data in a histogram.  It turns out (quite surprisingly) that the majority of restaurants score better than 94 and that 100 is the mode of the dataset.  This is actually quite comforting to know that so many restaurants score so well, but might make you think twice about eating at your favorite restaurant that happened to score a 90.</p>

<p><a href="http://zipfianacademy.com/maps/h3/histogram.png"><img src="http://media.tumblr.com/ed9ff69139fdb01dc4452ccda9469880/tumblr_inline_mqv5q9sbrH1qz4rgp.png" alt=""/></a></p>

<p>The right plot is a binning of the scores into the categories the city defined to give a more qualitative interpretation of the scores (‘Poor’, ‘Needs Improvement’, ‘Adequate’, and ‘Good’).  The interesting thing to note about these quantizations of the scores is that the scale is very nonlinear: 0 -&gt; 70, 71 -&gt; 85, 86 -&gt; 90, 91 -&gt; 100.</p>

<p><a href="http://zipfianacademy.com/maps/h3/quantiles.png"><img src="http://media.tumblr.com/c1b527657b3f2bc1e45d263fe8445736/tumblr_inline_mqv5qg9H8H1qz4rgp.png" alt=""/></a></p>

<p>With such a skewed distribution and nonlinear scales, often our old way of thinking does not directly translate.  To get a better grasp on the relative scores of restaurants compared to each other (and potential other cities) I computed the quantiles for the distribution.  This allows us to have a somewhat standardized ranking to compare different scales and distributions in normalized fashion.  It is for this reason that summary statistics can be quite powerful tools for inference and a standard tool in any statistician’s (or data scientist’s) tool belt.</p>

<p>Due to these very basic and easy to implement analyses, I am now a much more informed citizen and realize that scales in general can have subliminal/collateral effects on your perception of the rest of the world.  In school we come to internalize 70 as a passing score, anything better than 90 quite good, and 98-100 to be unheard of… for Berkeley Physics at least ;)</p>

<h3>Conclusion</h3>

<p>I hope this post showed you that you do not necessarily need to do very complex analyses to get interesting insights and inspires folks to get out there and start working with open data.  The first step to breaking into data science is to start making, and pick a project that you are passionate about (or always wanted to know the answer to).  If you have any question about restaurant health inspection data, the data science process, or our program and classes please do not hesitate to reach out (or to just say hello!) at <a href="mailto:jonathan@zipfianacademy.com" target="_blank">jonathan@zipfianacademy.com</a>.  Happy Data-ing!</p>

<p><strong><em>Cheers,</em></strong></p>

<p><strong><em>Jonathan</em></strong></p>
