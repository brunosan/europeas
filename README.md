# Get out to vote

This is a basic "Get out to vote" model, with a strategy to select places that would win extra seats
to the selected political party, according to the d'Hont rule. It uses real voting data for the
european elections in Spain in 2014.

## Strategy

Calculate which places vote less than average. Then of those, figure out which one, if they increase their participation to the average, would yield a net increase in votes to the designated party. Esentially which places would grant an extra electoral seat whith a non-partisan democratic message of more participation.


Example location:

![plot for Soto del Barco](https://github.com/brunosan/europeas/blob/master/plots/Soto%20del%20Barco-00.png?raw=true)

In the image above, each graph uses the same style. In blue is the national histogram, then in orange is a smoothed histogram using a gaussian kernel (this means that the integral is not 1, but it helps ientify the maximum of the histogram). In red is the value corresponding to the particular place of the graph. Top left shows the participation rate, then each row (left to right, top to bottom) shows the top 5 parties with most votes. In the titles of the graph is the party name, and the actual value of votes for that location (not the % as the x-axis). On the top left chart, the one with the participation rate, the title shows the census for that location. On the x-axis, I use the range 1 to 99 percentile to have prettier graphs. The y-axis is auto-scaled to the individual range.


## Hypothesis

1. The distribution of votes within a region doesn´t change with participation rates increases towards the average. I.e. if 30% of the people in that region vote for party X when the participation is 20%, they will still vote 30% to that party when the participation incerases to 30%.

2. We use a single electoral college or circumscription, as it the case for Spain and the European elections. I.e. every vote count the same, and the seats are assignes using the D´Hont law.

Note: To improve it, one could assume a partisan message, and then assign the effort to increase the % of votes for a particular vote using the analogy of saturation points. I.e. look at change of prevalence as a proxy of effort to make that change.

## Data

* I use the latest most granular data available for Spain 2014 european elections.

* The data is available online at http://elecciones.mir.es/resultados2014/ini99v.htm Note: They are "provisional to 99.77% but these are the best ones I could get at the municipal granularity.

* One could modify votes using more recent trends, but for the scope of this exercise, this is enough.

The data is not available in a standard format, rather is an internal file inside the executable program the goverment created. One has to figet a bit extract the `.mdb` database of 132 Mb.

To extract the data I use the hins given here: https://www.codeenigma.com/community/blog/using-mdbtools-nix-convert-microsoft-access-mysql

```sql
mdb-schema Europeas2014.mdb mysql > schema.sql
mkdir sql
for i in $( mdb-tables Europeas2014.mdb ); do echo $i ; mdb-export -D "%Y-%m-%d %H:%M:%S" -H -I mysql Europeas2014.mdb $i > sql/$i.sql; done
mysql -uroot -ppassword europeas < schema.sql
for i in $( ls sql/ ); do echo $i ; mysql -uroot -ppassword europeas < sql/$i ; done
```

Look at [Elecciones Europeas.ipynb](Elecciones%20Europeas.ipynb) for the entire code.
