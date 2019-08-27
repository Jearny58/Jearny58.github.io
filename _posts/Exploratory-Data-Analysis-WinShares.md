
# Exploratory Data Analysis: Win Shares


```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
%matplotlib inline

pd.set_option('display.width', 500)
pd.set_option('display.max_columns', 100)
```


```python
# import data
df = pd.read_csv('data/df_all.csv')
```


```python
df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Player</th>
      <th>Pos</th>
      <th>Age</th>
      <th>Tm</th>
      <th>G</th>
      <th>GS</th>
      <th>MP</th>
      <th>FG</th>
      <th>FGA</th>
      <th>FG%</th>
      <th>3P</th>
      <th>3PA</th>
      <th>3P%</th>
      <th>2P</th>
      <th>2PA</th>
      <th>2P%</th>
      <th>FT</th>
      <th>FTA</th>
      <th>FT%</th>
      <th>ORB</th>
      <th>DRB</th>
      <th>TRB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TOV</th>
      <th>PF</th>
      <th>PTS</th>
      <th>ORtg</th>
      <th>DRtg</th>
      <th>PER</th>
      <th>TS%</th>
      <th>3PAr</th>
      <th>FTr</th>
      <th>ORB%</th>
      <th>DRB%</th>
      <th>TRB%</th>
      <th>AST%</th>
      <th>STL%</th>
      <th>BLK%</th>
      <th>TOV%</th>
      <th>USG%</th>
      <th>OWS</th>
      <th>DWS</th>
      <th>WS</th>
      <th>WS/48</th>
      <th>OBPM</th>
      <th>DBPM</th>
      <th>BPM</th>
      <th>VORP</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alex Abrines</td>
      <td>SG</td>
      <td>24</td>
      <td>OKC</td>
      <td>75</td>
      <td>8</td>
      <td>1134</td>
      <td>5.0</td>
      <td>12.7</td>
      <td>0.395</td>
      <td>3.7</td>
      <td>9.7</td>
      <td>0.380</td>
      <td>1.4</td>
      <td>3.1</td>
      <td>0.443</td>
      <td>1.7</td>
      <td>2.0</td>
      <td>0.848</td>
      <td>1.1</td>
      <td>3.9</td>
      <td>5.0</td>
      <td>1.2</td>
      <td>1.7</td>
      <td>0.4</td>
      <td>1.1</td>
      <td>5.4</td>
      <td>15.4</td>
      <td>116.0</td>
      <td>110.0</td>
      <td>9.0</td>
      <td>0.567</td>
      <td>0.759</td>
      <td>0.158</td>
      <td>2.5</td>
      <td>8.9</td>
      <td>5.6</td>
      <td>3.4</td>
      <td>1.7</td>
      <td>0.6</td>
      <td>7.4</td>
      <td>12.7</td>
      <td>1.3</td>
      <td>1.0</td>
      <td>2.2</td>
      <td>0.094</td>
      <td>-0.5</td>
      <td>-1.7</td>
      <td>-2.2</td>
      <td>-0.1</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Quincy Acy</td>
      <td>PF</td>
      <td>27</td>
      <td>BRK</td>
      <td>70</td>
      <td>8</td>
      <td>1359</td>
      <td>4.6</td>
      <td>13.0</td>
      <td>0.356</td>
      <td>3.6</td>
      <td>10.4</td>
      <td>0.349</td>
      <td>1.0</td>
      <td>2.6</td>
      <td>0.384</td>
      <td>1.8</td>
      <td>2.1</td>
      <td>0.817</td>
      <td>1.4</td>
      <td>7.8</td>
      <td>9.2</td>
      <td>2.0</td>
      <td>1.2</td>
      <td>1.0</td>
      <td>2.1</td>
      <td>5.3</td>
      <td>14.7</td>
      <td>99.0</td>
      <td>110.0</td>
      <td>8.2</td>
      <td>0.525</td>
      <td>0.800</td>
      <td>0.164</td>
      <td>3.1</td>
      <td>17.1</td>
      <td>10.0</td>
      <td>6.0</td>
      <td>1.2</td>
      <td>1.6</td>
      <td>13.3</td>
      <td>14.4</td>
      <td>-0.1</td>
      <td>1.1</td>
      <td>1.0</td>
      <td>0.036</td>
      <td>-2.0</td>
      <td>-0.2</td>
      <td>-2.2</td>
      <td>-0.1</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Steven Adams</td>
      <td>C</td>
      <td>24</td>
      <td>OKC</td>
      <td>76</td>
      <td>76</td>
      <td>2487</td>
      <td>8.9</td>
      <td>14.2</td>
      <td>0.629</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000</td>
      <td>8.9</td>
      <td>14.2</td>
      <td>0.631</td>
      <td>3.2</td>
      <td>5.7</td>
      <td>0.559</td>
      <td>7.7</td>
      <td>6.0</td>
      <td>13.7</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>1.6</td>
      <td>2.6</td>
      <td>4.3</td>
      <td>21.1</td>
      <td>125.0</td>
      <td>107.0</td>
      <td>20.6</td>
      <td>0.630</td>
      <td>0.003</td>
      <td>0.402</td>
      <td>16.6</td>
      <td>13.9</td>
      <td>15.3</td>
      <td>5.5</td>
      <td>1.8</td>
      <td>2.8</td>
      <td>13.3</td>
      <td>16.7</td>
      <td>6.7</td>
      <td>3.0</td>
      <td>9.7</td>
      <td>0.187</td>
      <td>2.2</td>
      <td>1.1</td>
      <td>3.3</td>
      <td>3.3</td>
      <td>2018</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.tail(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Player</th>
      <th>Pos</th>
      <th>Age</th>
      <th>Tm</th>
      <th>G</th>
      <th>GS</th>
      <th>MP</th>
      <th>FG</th>
      <th>FGA</th>
      <th>FG%</th>
      <th>3P</th>
      <th>3PA</th>
      <th>3P%</th>
      <th>2P</th>
      <th>2PA</th>
      <th>2P%</th>
      <th>FT</th>
      <th>FTA</th>
      <th>FT%</th>
      <th>ORB</th>
      <th>DRB</th>
      <th>TRB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TOV</th>
      <th>PF</th>
      <th>PTS</th>
      <th>ORtg</th>
      <th>DRtg</th>
      <th>PER</th>
      <th>TS%</th>
      <th>3PAr</th>
      <th>FTr</th>
      <th>ORB%</th>
      <th>DRB%</th>
      <th>TRB%</th>
      <th>AST%</th>
      <th>STL%</th>
      <th>BLK%</th>
      <th>TOV%</th>
      <th>USG%</th>
      <th>OWS</th>
      <th>DWS</th>
      <th>WS</th>
      <th>WS/48</th>
      <th>OBPM</th>
      <th>DBPM</th>
      <th>BPM</th>
      <th>VORP</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4756</th>
      <td>Nick Young</td>
      <td>SG</td>
      <td>23</td>
      <td>WAS</td>
      <td>82</td>
      <td>5</td>
      <td>1837</td>
      <td>9.6</td>
      <td>21.7</td>
      <td>0.444</td>
      <td>1.6</td>
      <td>4.7</td>
      <td>0.341</td>
      <td>8.0</td>
      <td>17.0</td>
      <td>0.473</td>
      <td>4.7</td>
      <td>5.5</td>
      <td>0.850</td>
      <td>0.9</td>
      <td>3.4</td>
      <td>4.3</td>
      <td>2.7</td>
      <td>1.1</td>
      <td>0.6</td>
      <td>2.6</td>
      <td>4.0</td>
      <td>25.6</td>
      <td>105.0</td>
      <td>117.0</td>
      <td>13.1</td>
      <td>0.530</td>
      <td>0.217</td>
      <td>0.255</td>
      <td>1.9</td>
      <td>7.8</td>
      <td>4.8</td>
      <td>9.0</td>
      <td>1.1</td>
      <td>0.9</td>
      <td>9.9</td>
      <td>23.1</td>
      <td>1.2</td>
      <td>-0.1</td>
      <td>1.2</td>
      <td>0.030</td>
      <td>-1.1</td>
      <td>-3.8</td>
      <td>-4.9</td>
      <td>-1.3</td>
      <td>2009</td>
    </tr>
    <tr>
      <th>4757</th>
      <td>Thaddeus Young</td>
      <td>PF</td>
      <td>20</td>
      <td>PHI</td>
      <td>75</td>
      <td>71</td>
      <td>2580</td>
      <td>9.8</td>
      <td>19.9</td>
      <td>0.495</td>
      <td>1.2</td>
      <td>3.4</td>
      <td>0.341</td>
      <td>8.7</td>
      <td>16.5</td>
      <td>0.526</td>
      <td>2.9</td>
      <td>3.9</td>
      <td>0.735</td>
      <td>2.9</td>
      <td>4.9</td>
      <td>7.8</td>
      <td>1.8</td>
      <td>2.1</td>
      <td>0.5</td>
      <td>2.4</td>
      <td>3.4</td>
      <td>23.7</td>
      <td>110.0</td>
      <td>109.0</td>
      <td>15.3</td>
      <td>0.549</td>
      <td>0.171</td>
      <td>0.197</td>
      <td>6.4</td>
      <td>11.1</td>
      <td>8.8</td>
      <td>5.8</td>
      <td>2.1</td>
      <td>0.7</td>
      <td>10.2</td>
      <td>20.5</td>
      <td>3.0</td>
      <td>2.5</td>
      <td>5.5</td>
      <td>0.102</td>
      <td>0.8</td>
      <td>-0.2</td>
      <td>0.5</td>
      <td>1.7</td>
      <td>2009</td>
    </tr>
    <tr>
      <th>4758</th>
      <td>Sun Yue</td>
      <td>SG</td>
      <td>23</td>
      <td>LAL</td>
      <td>10</td>
      <td>0</td>
      <td>28</td>
      <td>5.5</td>
      <td>20.0</td>
      <td>0.273</td>
      <td>0.0</td>
      <td>10.9</td>
      <td>0.000</td>
      <td>5.5</td>
      <td>9.1</td>
      <td>0.600</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>3.6</td>
      <td>1.8</td>
      <td>1.8</td>
      <td>5.5</td>
      <td>18.2</td>
      <td>10.9</td>
      <td>53.0</td>
      <td>108.0</td>
      <td>-8.6</td>
      <td>0.273</td>
      <td>0.545</td>
      <td>0.000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>9.8</td>
      <td>1.8</td>
      <td>2.8</td>
      <td>21.4</td>
      <td>22.0</td>
      <td>-0.2</td>
      <td>0.0</td>
      <td>-0.1</td>
      <td>-0.226</td>
      <td>-11.4</td>
      <td>-3.7</td>
      <td>-15.2</td>
      <td>-0.1</td>
      <td>2009</td>
    </tr>
  </tbody>
</table>
</div>



## Background Info on Columns (i.e. Variables)

- __Pos__ -- Position
- __Age__ -- Age of Player at the start of February 1st of that season.
- __Tm__ -- Team
- __G__ -- Games
- __MP__ -- Minutes Played
- __FG__ -- Field Goals Per 100 Team Possessions
- __FGA__ -- Field Goal Attempts Per 100 Team Possessions
- __FG%__ -- Field Goal Percentage
- __3P__ -- 3-Point Field Goals Per 100 Team Possesssions
- __3PA__ -- 3-Point Field Goal Attempts Per 100 Team Possessions
- __3P%__ -- FG% on 3-Pt FGAs.
- __2P__ -- 2-Point Field Goals Per 100 Team Possessions
- __2PA__ -- 2-Point Field Goal Attempts Per 100 Team Possessions
- __2P%__ -- FG% on 2-Pt FGAs.
- __FT__ -- Free Throws Per 100 Team Possessions
- __FTA__ -- Free Throw Attempts Per 100 Team Possessions
- __FT%__ -- Free Throw Percentage
- __ORB__ -- Offensive Rebounds Per 100 Team Possessions
- __DRB__ -- Defensive Rebounds Per 100 Team Possessions
- __TRB__ -- Total Rebounds Per 100 Team Possessions
- __AST__ -- Assists Per 100 Team Possessions
- __STL__ -- Steals Per 100 Team Possessions
- __BLK__ -- Blocks Per 100 Team Possessions
- __TOV__ -- Turnovers Per 100 Team Possessions
- __PF__ -- Personal Fouls Per 100 Team Possessions
- __PTS__ -- Points Per 100 Team Possessions
- __ORtg__ -- Offensive Rating: An estimate of points produced (players) or scored (teams) per 100 possessions
- __DRtg__ -- Defensive Rating: An estimate of points allowed per 100 possessions
- __PER__ -- Player Efficiency Rating: A measure of per-minute production standardized such that the league average is 15.
- __TS%__ -- True Shooting Percentage: A measure of shooting efficiency that takes into account 2-point field goals, 3-point field goals, and free throws.
- __3PAr__ -- 3-Point Attempt Rate: Percentage of FG Attempts from 3-Point Range
- __FTr__ -- Free Throw Attempt Rate: Number of FT Attempts Per FG Attempt
- __ORB%__ -- Offensive Rebound Percentage: An estimate of the percentage of available offensive rebounds a player grabbed while he was on the floor.
- __DRB%__ -- Defensive Rebound Percentage: An estimate of the percentage of available defensive rebounds a player grabbed while he was on the floor.
- __TRB%__ -- Total Rebound Percentage: An estimate of the percentage of available rebounds a player grabbed while he was on the floor.
- __AST%__ -- Assist Percentage: An estimate of the percentage of teammate field goals a player assisted while he was on the floor.
- __STL%__ -- Steal Percentage: An estimate of the percentage of opponent possessions that end with a steal by the player while he was on the floor.
- __BLK%__ -- Block Percentage: An estimate of the percentage of opponent two-point field goal attempts blocked by the player while he was on the floor.
- __TOV%__ -- Turnover Percentage: An estimate of turnovers committed per 100 plays.
- __USG%__ -- Usage Percentage: An estimate of the percentage of team plays used by a player while he was on the floor.
- __OWS__ -- Offensive Win Shares: An estimate of the number of wins contributed by a player due to his offense.
- __DWS__ -- Defensive Win Shares: An estimate of the number of wins contributed by a player due to his defense.
- __WS__ -- Win Shares: An estimate of the number of wins contributed by a player.
- __WS/48__ -- Win Shares Per 48 Minutes: An estimate of the number of wins contributed by a player per 48 minutes (league average is approximately .100)
- __OBPM__ -- Offensive Box Plus/Minus: A box score estimate of the offensive points per 100 possessions a player contributed above a league-average player, translated to an average team.
- __DBPM__ -- Defensive Box Plus/Minus: A box score estimate of the defensive points per 100 possessions a player contributed above a league-average player, translated to an average team.
- __BPM__ -- Box Plus/Minus: A box score estimate of the points per 100 possessions a player contributed above a league-average player, translated to an average team.
- __VORP__ -- Value over Replacement Player: A box score estimate of the points per 100 TEAM possessions that a player contributed above a replacement-level (-2.0) player, translated to an average team and prorated to an 82-game season.

## General Overview


```python
# get a general statistical overview of data
df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>G</th>
      <th>GS</th>
      <th>MP</th>
      <th>FG</th>
      <th>FGA</th>
      <th>FG%</th>
      <th>3P</th>
      <th>3PA</th>
      <th>3P%</th>
      <th>2P</th>
      <th>2PA</th>
      <th>2P%</th>
      <th>FT</th>
      <th>FTA</th>
      <th>FT%</th>
      <th>ORB</th>
      <th>DRB</th>
      <th>TRB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TOV</th>
      <th>PF</th>
      <th>PTS</th>
      <th>ORtg</th>
      <th>DRtg</th>
      <th>PER</th>
      <th>TS%</th>
      <th>3PAr</th>
      <th>FTr</th>
      <th>ORB%</th>
      <th>DRB%</th>
      <th>TRB%</th>
      <th>AST%</th>
      <th>STL%</th>
      <th>BLK%</th>
      <th>TOV%</th>
      <th>USG%</th>
      <th>OWS</th>
      <th>DWS</th>
      <th>WS</th>
      <th>WS/48</th>
      <th>OBPM</th>
      <th>DBPM</th>
      <th>BPM</th>
      <th>VORP</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.00000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
      <td>4759.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>26.514814</td>
      <td>52.745955</td>
      <td>25.339357</td>
      <td>1225.296281</td>
      <td>7.217966</td>
      <td>16.428157</td>
      <td>0.438860</td>
      <td>1.471759</td>
      <td>4.346123</td>
      <td>0.251930</td>
      <td>5.746627</td>
      <td>12.081635</td>
      <td>0.468650</td>
      <td>3.236520</td>
      <td>4.438643</td>
      <td>0.702132</td>
      <td>2.444526</td>
      <td>6.631876</td>
      <td>9.075436</td>
      <td>4.100420</td>
      <td>1.564762</td>
      <td>1.061294</td>
      <td>2.853562</td>
      <td>4.883652</td>
      <td>19.144295</td>
      <td>103.206136</td>
      <td>107.242068</td>
      <td>12.908615</td>
      <td>0.516146</td>
      <td>0.258803</td>
      <td>0.282542</td>
      <td>5.411599</td>
      <td>14.719353</td>
      <td>10.070708</td>
      <td>12.700000</td>
      <td>1.564635</td>
      <td>1.63858</td>
      <td>13.637046</td>
      <td>18.642383</td>
      <td>1.347342</td>
      <td>1.245955</td>
      <td>2.593717</td>
      <td>0.075723</td>
      <td>-1.423198</td>
      <td>-0.377222</td>
      <td>-1.799832</td>
      <td>0.618281</td>
      <td>2013.641521</td>
    </tr>
    <tr>
      <th>std</th>
      <td>4.234079</td>
      <td>24.680062</td>
      <td>28.711685</td>
      <td>865.710724</td>
      <td>2.706718</td>
      <td>5.017250</td>
      <td>0.097527</td>
      <td>1.572987</td>
      <td>3.764628</td>
      <td>0.172592</td>
      <td>2.772803</td>
      <td>5.010525</td>
      <td>0.105202</td>
      <td>2.077209</td>
      <td>2.704497</td>
      <td>0.197685</td>
      <td>2.323574</td>
      <td>3.044216</td>
      <td>4.654666</td>
      <td>2.960423</td>
      <td>0.876248</td>
      <td>1.096641</td>
      <td>1.399177</td>
      <td>2.318563</td>
      <td>7.078491</td>
      <td>16.773901</td>
      <td>4.855654</td>
      <td>6.337496</td>
      <td>0.096518</td>
      <td>0.217302</td>
      <td>0.221244</td>
      <td>4.965536</td>
      <td>6.696185</td>
      <td>5.176888</td>
      <td>9.394725</td>
      <td>0.875962</td>
      <td>1.69300</td>
      <td>6.117325</td>
      <td>5.356425</td>
      <td>2.013593</td>
      <td>1.155710</td>
      <td>2.894824</td>
      <td>0.099410</td>
      <td>3.620930</td>
      <td>2.168702</td>
      <td>4.342013</td>
      <td>1.318049</td>
      <td>2.878309</td>
    </tr>
    <tr>
      <th>min</th>
      <td>19.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-41.100000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-3.300000</td>
      <td>-0.200000</td>
      <td>-2.100000</td>
      <td>-1.065000</td>
      <td>-38.900000</td>
      <td>-23.100000</td>
      <td>-59.000000</td>
      <td>-1.600000</td>
      <td>2009.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>23.000000</td>
      <td>33.000000</td>
      <td>1.000000</td>
      <td>419.500000</td>
      <td>5.600000</td>
      <td>13.100000</td>
      <td>0.400000</td>
      <td>0.000000</td>
      <td>0.300000</td>
      <td>0.000000</td>
      <td>3.900000</td>
      <td>8.600000</td>
      <td>0.432000</td>
      <td>1.800000</td>
      <td>2.600000</td>
      <td>0.645000</td>
      <td>0.900000</td>
      <td>4.500000</td>
      <td>5.500000</td>
      <td>2.100000</td>
      <td>1.100000</td>
      <td>0.300000</td>
      <td>2.000000</td>
      <td>3.500000</td>
      <td>14.800000</td>
      <td>98.000000</td>
      <td>105.000000</td>
      <td>9.900000</td>
      <td>0.488000</td>
      <td>0.019000</td>
      <td>0.169000</td>
      <td>2.000000</td>
      <td>9.900000</td>
      <td>6.100000</td>
      <td>6.300000</td>
      <td>1.100000</td>
      <td>0.50000</td>
      <td>10.300000</td>
      <td>14.900000</td>
      <td>0.000000</td>
      <td>0.300000</td>
      <td>0.300000</td>
      <td>0.038000</td>
      <td>-3.000000</td>
      <td>-1.600000</td>
      <td>-3.600000</td>
      <td>-0.100000</td>
      <td>2011.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>26.000000</td>
      <td>60.000000</td>
      <td>11.000000</td>
      <td>1158.000000</td>
      <td>7.100000</td>
      <td>16.200000</td>
      <td>0.440000</td>
      <td>1.300000</td>
      <td>4.300000</td>
      <td>0.313000</td>
      <td>5.500000</td>
      <td>11.700000</td>
      <td>0.476000</td>
      <td>2.900000</td>
      <td>4.000000</td>
      <td>0.750000</td>
      <td>1.700000</td>
      <td>6.100000</td>
      <td>8.000000</td>
      <td>3.200000</td>
      <td>1.500000</td>
      <td>0.700000</td>
      <td>2.700000</td>
      <td>4.400000</td>
      <td>18.800000</td>
      <td>105.000000</td>
      <td>108.000000</td>
      <td>12.900000</td>
      <td>0.528000</td>
      <td>0.257000</td>
      <td>0.248000</td>
      <td>3.800000</td>
      <td>13.600000</td>
      <td>8.900000</td>
      <td>9.800000</td>
      <td>1.500000</td>
      <td>1.10000</td>
      <td>12.900000</td>
      <td>18.200000</td>
      <td>0.700000</td>
      <td>0.900000</td>
      <td>1.700000</td>
      <td>0.082000</td>
      <td>-1.200000</td>
      <td>-0.400000</td>
      <td>-1.400000</td>
      <td>0.100000</td>
      <td>2014.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>29.000000</td>
      <td>74.000000</td>
      <td>51.000000</td>
      <td>1951.500000</td>
      <td>8.700000</td>
      <td>19.600000</td>
      <td>0.485000</td>
      <td>2.500000</td>
      <td>7.100000</td>
      <td>0.368000</td>
      <td>7.300000</td>
      <td>15.200000</td>
      <td>0.517000</td>
      <td>4.300000</td>
      <td>5.800000</td>
      <td>0.820000</td>
      <td>3.700000</td>
      <td>8.400000</td>
      <td>12.100000</td>
      <td>5.500000</td>
      <td>2.000000</td>
      <td>1.500000</td>
      <td>3.500000</td>
      <td>5.800000</td>
      <td>23.100000</td>
      <td>111.000000</td>
      <td>110.000000</td>
      <td>16.000000</td>
      <td>0.563000</td>
      <td>0.419000</td>
      <td>0.348000</td>
      <td>8.200000</td>
      <td>18.700000</td>
      <td>13.400000</td>
      <td>16.900000</td>
      <td>2.000000</td>
      <td>2.30000</td>
      <td>16.100000</td>
      <td>22.000000</td>
      <td>2.100000</td>
      <td>1.900000</td>
      <td>4.000000</td>
      <td>0.121000</td>
      <td>0.500000</td>
      <td>0.900000</td>
      <td>0.600000</td>
      <td>1.000000</td>
      <td>2016.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>42.000000</td>
      <td>83.000000</td>
      <td>82.000000</td>
      <td>3269.000000</td>
      <td>52.600000</td>
      <td>52.600000</td>
      <td>1.000000</td>
      <td>50.100000</td>
      <td>50.100000</td>
      <td>1.000000</td>
      <td>52.600000</td>
      <td>52.600000</td>
      <td>1.000000</td>
      <td>22.300000</td>
      <td>33.800000</td>
      <td>1.000000</td>
      <td>52.600000</td>
      <td>49.300000</td>
      <td>76.900000</td>
      <td>19.600000</td>
      <td>12.500000</td>
      <td>16.800000</td>
      <td>20.900000</td>
      <td>50.400000</td>
      <td>150.400000</td>
      <td>300.000000</td>
      <td>121.000000</td>
      <td>133.800000</td>
      <td>1.500000</td>
      <td>1.000000</td>
      <td>6.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>86.400000</td>
      <td>78.500000</td>
      <td>12.500000</td>
      <td>26.30000</td>
      <td>100.000000</td>
      <td>47.800000</td>
      <td>14.800000</td>
      <td>7.700000</td>
      <td>20.300000</td>
      <td>2.712000</td>
      <td>68.600000</td>
      <td>17.100000</td>
      <td>54.400000</td>
      <td>12.400000</td>
      <td>2018.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# function that creates histograms
def create_histogram(df, var1, plot_title):
    plt.figure(figsize = (10, 6))
    df[var1].plot(kind = 'hist', edgecolor = 'black')
    plt.xlabel(var1)
    plt.title(plot_title, size = 'x-large')
```


```python
# create histogram of games played in (G)
create_histogram(df, 'G', 'Games Played In');
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_9_0.png)



```python
# histogram of G (games played in)
plt.figure(figsize = (10, 6))
df['G'].plot(kind = 'hist', bins = 82, edgecolor = 'black')
plt.xlabel('Games')
plt.title('Games Played In', size = 'x-large');
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_10_0.png)


__OBSERVATION__: Why did I decide to produce two histograms of Games Played In? While the first one does get the point across -- that the number of observations seems to increase significantly around the 60 game mark -- I wanted to get a little more nuanced. Since there are 82 games in a season, I decided to create another plot with 82 bins, which would allow for an exact representation of the number of games, from 0-82, that players played in. 

As we can see, the number of observations stays roughly the same from zero games to about 50 games then it begins to really pick up around 60 games. What I'm hypothesizing is that the may provide evidence that while teams have between 13-15 players on their active roster on any given night, that only a portion of these players actually get playing time. 


```python
# histogram of games started
plt.figure(figsize = (10, 6))
df['GS'].plot(kind = 'hist', bins = 82, edgecolor = 'black')
plt.xlabel('Games')
plt.title('Games Started', size = 'x-large');
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_12_0.png)


__OBSERVATION__: I was not expecting this when it came to the number of games started! I was expecting something similar to the games played in but when you take a step back you realize that this doesn't really make sense. In the NBA, there tends to be a core group of five players that are the starters, with 2-3 other players that serve as the 'first subs' so to speak. These 2-3 players may only start sparingly (or not at all) but still get a signficant amount of playing time off the bench. 


```python
# histogram of minutes played
plt.figure(figsize = (10, 6))
df['MP'].plot(kind = 'hist', bins = 30, edgecolor = 'black')
plt.xlabel('Minutes')
plt.title('Total Minutes Played', size = 'x-large');
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_14_0.png)



```python
# change Pos column to categorical 
df['Pos'] = df['Pos'].astype('category')
```


```python
# import seaborn for countplot
import seaborn as sns

f, ax = plt.subplots(figsize=(10, 6))
sns.countplot(x="Pos", data=df, ax=ax, palette='Set1').set_title('Positional Breakdown');
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_16_0.png)


__OBSERVATION__: In regards to the histogram of minutes played, we'll need to keep this in mind once we start diving deeper into `win shares` because there looks to be a decent amount of observations that do not have significant playing time (i.e. < 500 minutes played). I could see these having the potential to skew the analysis because the players may have overperformed relative to their skill set. 

What do I mean by this? Say you have a player that only played 100 minutes total the whole season but scored 50 points. His impact is pretty significant relative to the total number of minute he played. However, given more playing time his stats would most likely revert to the mean or potentially even below the mean.


```python
# get breakdown by team
df['Tm'] = df['Tm'].astype('category')
plt.figure(figsize = (12, 10))
df['Tm'].value_counts().plot(kind = 'barh', color='steelblue', edgecolor='black')
plt.xlabel('Count')
plt.ylabel('Team')
plt.title('Total Players By Team');
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_18_0.png)


__OBSERVATION__: While not required for our analysis, I wanted to check out the breakdown by team. You may be wondering what team does `TOT` represent? Well `TOT` is indicative of a player who played for multiple teams during that particular season, and as a result the stats for that particular player are his total from the whole season. From the graph above, we can see that there were quite a few players who played on multiple teams during one season.

## Focus: Win Shares

#### Background

In basketball, like most other sports, per game statistics are used widely to evaluate individual player's and their respective skill set. Things like points per game, shooting percentage or rebounds per game are often thrown around in arguments by experts and fans alike to show why one player is 'better' than the other. Yet, these arguments are one-dimensional in that they are only a small part of the picture and can be (and often are) tainted by the 'eye' test, biasing us towards one player simply because of he is on our favorite team.

This brings us to the question: is there any statistic available today that can help us more definitively assess a player's contribution to a team's success? There is, and it is called `Win Shares`. 

What is `Win Shares` you may ask? The name says it all; it is a statistic that quantifies how much a player contributes to winning, which is the ultimate goal of every NBA team. 

It can further be broken down into offensive and defensive win shares. For those interested, below are the equations for both offensive and defensive win shares.

- __OFFENSIVE WIN SHARES__ = ((POINTS PRODUCED) - 0.92 x (LEAGUE POINTS PER POSSESSION) x (OFFENSIVE POSSESSIONS)) / (0.32 x (LEAGUE POINTS PER GAME) x ((TEAM PACE) / (LEAGUE PACE)))

- __DEFENSIVE WIN SHARES__ = ((PLAYER MINUTES PLAYED / TEAM MINUTES PLAYED) x (TEAM DEFENSIVE POSSESSIONS) x (1.08 x (LEAGUE POINTS PER POSSESSION) - ((DEFENSIVE RATING) / 100))) / (0.32 X (LEAGUE POINTS PER GAME) X ((TEAM PACE) / (LEAGUE PACE))) 

These two are then added together to generate an overall win shares statistic. As you can see `Win Shares` is more of an all-encompassing statistic than say points per game. Sure, we can see how awesome (on average) a player is on the offensive side of the floor but what does that tell us about his defense? Nothing. Because `Win Shares` takes into account an assortment of statistics from both the offensive and defensive end of the floor and thus gives us a better picture of an individual player's skillset, and more importantly, his ability to help a team win!

Now I also want to be upfront: win shares is not a 'perfect' statistic. It is not 100% accurate, as there are instances where a team's sum of `Win Shares` (from each indvidual player) is not _exactly_ equal to their actual number of total wins. While this may burst some people's bubble, this particular statistic is still remarkably useful and accurate; the average absolute error is 2.74 wins and this is dating back to the 1962-1963 season! It's as the saying goes, "All models are wrong, but some are useful!". So while not perfect, it serves as a __very__ good model that allows us to gain a good picture for a player's overall skill set.

So without further ado, let's explore the data!

PS - I found a cool video that gives a great overview of what `Win Shares` is, you can check it out [here](https://www.youtube.com/watch?v=jCohKyjipUA)!


```python
# generate function that creates a regression plot

def eda_regplot(df, x, y, title):
    '''Seaborn regression plot between two variables'''
    f, ax = plt.subplots(figsize=(10, 6))
    ax = sns.regplot(x=x, y=y, data=df, 
            ax=ax, scatter_kws={'alpha':0.5, 'color':'steelblue'}, line_kws={"color": "orange"})
    ax.set_title(title);
    
    
def eda_scatterplot(df, x, y, title):
    '''Seaborn scatter plot between two variables chosen from the data set'''
    f, ax = plt.subplots(figsize=(12, 8))
    ax = sns.regplot(x=x, y=y, data=df, 
            ax=ax, fit_reg=False, scatter_kws={'alpha':0.5, 'color':'steelblue'})
    ax.set_title(title)
    
```


```python
# import seaborn package to create visualizations
import seaborn as sns
```


```python
# scatter plot of Minutes Played and Win Shares
eda_scatterplot(df, 'MP', 'WS/48', 'Minutes Played and Win Shares')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_25_0.png)



```python
# Scatter plot of field goals made and win shares
eda_scatterplot(df, 'FG', 'WS/48', 'Field Goals Made & Win Shares')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_26_0.png)



```python
# scatter plot of 3-point field goals made and win shares
eda_scatterplot(df, '3P', 'WS/48', '3-point FG & Win Shares')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_27_0.png)


__OBSERVATION__: It looks like we have some outliers to deal with. Let's address them first before going any further.

#### Addressing Outliers

This is going to bring me to another useful statistic available to us: `PER` or player efficiency rating. In a way, `PER` is similar to `Win Shares` in that it tries to give an overall picture of a player's statistical contribution but from a slightly different angle (if your interested you can read more about it [here](https://www.basketball-reference.com/about/per.html).

So why are we going to use `PER`? Well `PER` has a [cut-off](http://insider.espn.com/nba/hollinger/statistics) (see 'Glossary') so to speak, which is that a player must have played 6.09 minutes per game (or 500 minutes total for the season). However, so we're going to use this minutes per game metric to see if we can't address the issue of potential outliers. 


```python
per_qualify = df['MP'] / df['G'] >= 6.09
```


```python
# create dataframe that includes players that averaged at least 6.09 minutes per game
df_per_qualify = df[per_qualify]
print('This is the shape of the original data frame: ', df.shape)
print('This is the shape of the data frame with players that averaged at least 6.09 min/ game: ', df_per_qualify.shape)
print('We eliminated ', abs(df.shape[0] - df_per_qualify.shape[0]) / (df.shape[0]) * 100, '% of the observations.' )

```

    This is the shape of the original data frame:  (4759, 51)
    This is the shape of the data frame with players that averaged at least 6.09 min/ game:  (4433, 51)
    We eliminated  6.85017860895146 % of the observations.


Overall, this didn't put a huge dent in the total number of observations, which is a good first sign. We'll take a deeper dive to see if it had any impact on addressing outliers within the data.


```python
# recreate scatter plot of minutes played and win shares
eda_scatterplot(df_per_qualify, 'MP', 'WS/48', 'Minutes Played & Win Shares')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_33_0.png)



```python
# recreate scatter plot of field goals made and win shares
eda_scatterplot(df_per_qualify, 'FG', 'WS', 'Field Goals Made & Win Shares')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_34_0.png)



```python
# scatter plot of 3-point field goals made and win shares
eda_scatterplot(df_per_qualify, '3P', 'WS', '3-point FG & Win Shares')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_35_0.png)



```python
# scatter plot of 2-point field goals made and win shares
eda_scatterplot(df_per_qualify, '2P', 'WS', '2-point FG & Win Shares')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_36_0.png)


__OBSERVATION__: When we compare the graphs above to the ones generated from the original `df`, we can see that this `PER` cut-off seemed to do a good job of eliminating outliers which in turn can allow us to move forward and (hopefully) get a better idea of what the data looks like visually. 

## Direction From Here

Up to this point we've been using `Win Shares` however, this may not be the most appropriate dependent variable in our data set. `Win Shares` is a players cumulative total over the whole season, but we're working with mostly per 100 possession data so it doesn't make sense to go this route. Instead, we're going to see the relationships that this data set has with the `WS/48` variable.

`WS/48` is an estimate of the number of wins contributed by a player per 48 minutes (league average is approximately .100) [(definition from basketball-reference.com)](https://www.basketball-reference.com/leagues/NBA_2019_advanced.html).

Further, we'll be focusing on certain segments of the game and their relationship with `WS/48`. For example, the first section with be focused on overall field goals (i.e. `FG`, `FGA`, `FG%`, `TS%`), the second will be focused on 3-point field goals and so on. Above each section will detail the variables of interest and their definitions (to give a little context and understanding for those that may not be familiar with the acronymns and their meanings).

Without further ado, let's dive in!

## __FOCUS__: `FG`, `FGA`, `FG%`, `TS%`

- __FG__: Field Goals Per 100 Team Possessions
- __FGA__: Field Goal Attempts Per 100 Team Possessions
- __FG%__: Field Goal Percentage (percentage of time a player makes a field goal)
- __TS%__: A measure of shooting efficiency that takes into account 2-point field goals, 3-point field goals, and free throws


```python
# scatter plot of field goals and WS/48
eda_scatterplot(df_per_qualify, 'FG', 'WS/48', 'Field Goals per 100 Possessions and Win Shares per 48 minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_40_0.png)



```python
# scatter plot of field goal attempts and WS/48
eda_scatterplot(df_per_qualify, 'FGA', 'WS/48', 'Field Goal Attempts per 100 Possessions and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_41_0.png)



```python
# scatter plot of field goal % and WS/48
eda_scatterplot(df_per_qualify, 'FG%', 'WS/48', 'Field Goal % and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_42_0.png)



```python
# scatter plot of true shooting % and WS/48
eda_scatterplot(df_per_qualify, 'TS%', 'WS/48', 'True Shooting % and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_43_0.png)


__OBSERVATION__: `FG` and `FGA` are ambiguous in their relationship with `WS/48`. You could say that there's a _slight_ positive relationship between `FG` and `WS/48` but that would be stretching it. However as we look at `FG%` and `TS%`, a relationship begins to take shape. 

Generally speaking it appears that the higher your shooting percentage -- `FG%` or `TS%` -- the higher your win share per 48 minutes is going to be. This seems pretty logical; having a higher shooting percentage makes you a more efficient shooter, giving your team more points in less possessions while having the additional benefit of giving the other team less opportunities to score themselves (via missed shots and more possessions for the opposing team). 

I also want to point out really quick what appears to be a slight anamoly in the `FG%` scatter plot. If you look at around the 0.50 `FG%` mark, you see that there is a smaller group of observations that appear to all shoot around the 50% mark (some being slightly over and others slightly under) but have a higher `WS/48`. This should alert us to the fact that basketball is not all about scoring points! There are multiple facets to the game and it looks like these players are helping their teams win in more ways than just putting the ball through the hoop.

## __FOCUS__: `3P`, `3PA`, `3P%`, and `3PAr`

- __3P__: 3-Point Field Goals Per 100 Team Possesssions
- __3PA__: 3-Point Field Goal Attempts Per 100 Team Possessions
- __3P%__: FG% on 3-Pt FGAs
- __3PAr__: Percentage of FG Attempts from 3-Point Range


```python
# scatter plot of 3P and WS/48
eda_scatterplot(df_per_qualify, '3P', 'WS/48', '3-Point FGs per 100 Team Possessions and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_46_0.png)



```python
# scatter plot of 3PA and WS/48
eda_scatterplot(df_per_qualify, '3PA', 'WS/48', '3-Point FGAs per 100 Team Possessions and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_47_0.png)



```python
# scatter plot of 3P% and WS/48
eda_scatterplot(df_per_qualify, '3P%', 'WS/48', '3-Point FG% per 100 Team Possessions and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_48_0.png)



```python
# scatter plot of 3PAr and WS/48
eda_scatterplot(df_per_qualify, '3PAr', 'WS/48', 'Percentage of FG Attempts from 3-Point Range and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_49_0.png)


__OBSERVATION__: Over the past few years, 3-point shots have become quite popular throughout the NBA. Teams are spreading the floor, creating a disadvantage for defenses in that they have more ground to cover with the same number of players. This can lead to open shots, particularly those from behind the arc (i.e. 3-point line). Teams like the Golden State Warriors almost live and die off of long-range shooting, and this has become a trend amongst many other NBA teams, which is why I am a little surprised at the results above. 

As we can see from the four statistics related with 3-point shooting, there doesn't appear to be any correlation between shooting 3-point shots and a player's contribution to his team winning. Plus there appears to be a significant portion of players that very rarely if ever take long-range shots. 

Let's move closer to the basket a little bit and take a look at closer range shots next.

## __FOCUS__: `2P`, `2PA`, `2P%`

- __2P__: 2-Point Field Goals Per 100 Team Possessions
- __2PA__: 2-Point Field Goal Attempts Per 100 Team Possessions
- __2P%__: FG% on 2-Pt FGAs


```python
eda_scatterplot(df_per_qualify, '2P', 'WS/48', '2-Point FGs Per 100 Possessions and Win Shares Per 48 Minutes')
eda_scatterplot(df_per_qualify, '2PA', 'WS/48', '2-Point FG Attempts Per 100 Possessions and Win Shares Per 48 Minutes')
eda_scatterplot(df_per_qualify, '2P%', 'WS/48', '2-Point FG% Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_52_0.png)



![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_52_1.png)



![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_52_2.png)


__OBSERVATION__: The first two scatter plots -- `2P` and `2PA` -- with relation to `WS/48` are ambiguous at best in their relationship. However, when we get to the `2P%` plot, which is the percentage of 2-point field goals made per 100 possessions, we can see the relationship gets quite a bit stronger. 

The values are condensed between approximately 0.35 and 0.60, with the number of `WS/48` increasing as `2P%` increases. Together with the previous observations of `FG%` and `TS%`, there appears to be mounting evidence that percentages may do a better job of explaining the relationship between shooting and win shares than the number of field goals made/attempted does. 

## __FOCUS__: `FT`, `FTA`, `FT%` and `FTr`

- __FT__: Free Throws Per 100 Team Possessions
- __FTA__: Free Throw Attempts Per 100 Team Possessions
- __FT%__: Free Throw Percentage
- __FTr__: Number of FT Attempts Per FG Attempt


```python
eda_scatterplot(df_per_qualify, 'FT', 'WS/48', 'Free Throws Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_55_0.png)



```python
eda_scatterplot(df_per_qualify, 'FTA', 'WS/48', 'Free Throw Attempts Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_56_0.png)



```python
eda_scatterplot(df_per_qualify, 'FT%', 'WS/48', 'Free Throw % Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_57_0.png)



```python
eda_scatterplot(df_per_qualify, 'FTr', 'WS/48', 'Number of FT Attempts per FG Attempt and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_58_0.png)


__OBSERVATION__: Another rather ambigous realtionship. However, there is something of note in the last graph between `FTr` and `WS/48`. While somewhat skewed by outliers, the steepness of the condensed group of observations as they go from 0 to 1 is interesting. 

`FTr` represents the number of FT attempts per FG attempts. For example, if a player were to take 1 shot (irregardless if they make it or not) then get fouled and take 2 FT attempts, his `FTr` would be 2 (2 FTA / 1 FGA).

Now back to the graph. The steepness indicates that player's who get to the free throw line more often (in respect to their field goal attempts) may contribute more to a team's chances of winning. This makes sense; free throws, due to the high percentage of success, are essentially free points in that you are given an open shot without the worries of any defenders trying to block the shot. 

An additional 'externality', if you want to call it that, is that it also contributes towards an opposing player's foul total. As a player, if you reach 6 fouls in a game, you 'foul out'. Essentially, you are kicked out of the game and not allowed to return for the remaining duration. If one of the opposing starters fouls out of the game, this could have a signficant impact on a team's chances of winning as the other team will now have to rely on bench players to fill the gap of their starter. 

## __FOCUS__: `ORB`, `ORB%`, `DRB`, `DRB%`, `TRB`, and `TRB%`

- __ORB__: Offensive Rebounds Per 100 Team Possessions
- __ORB%__: An estimate of the percentage of available offensive rebounds a player grabbed while he was on the floor.
- __DRB__: Defensive Rebounds Per 100 Team Possessions
- __DRB%__: An estimate of the percentage of available defensive rebounds a player grabbed while he was on the floor.
- __TRB__: Total Rebounds Per 100 Team Possessions
- __TRB%__: An estimate of the percentage of available rebounds a player grabbed while he was on the floor.


```python
eda_scatterplot(df_per_qualify, 'ORB', 'WS/48', 'Offensive Rebounds Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_61_0.png)



```python
eda_scatterplot(df_per_qualify, 'ORB%', 'WS/48', 'Offensive Rebound % and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_62_0.png)



```python
eda_scatterplot(df_per_qualify, 'DRB', 'WS/48', 'Defensive Rebounds Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_63_0.png)



```python
eda_scatterplot(df_per_qualify, 'DRB%', 'WS/48', 'Defensive Rebound % and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_64_0.png)



```python
eda_scatterplot(df_per_qualify, 'TRB', 'WS/48', 'Total Rebounds Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_65_0.png)



```python
eda_scatterplot(df_per_qualify, 'TRB%', 'WS/48', 'Total Rebound % and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_66_0.png)


__OBSERVATION__: Overall, rebounding doesn't look like it factors in all that much. The scatter plots in this section all look relatively similar. 

If there are any takeaways from this particular section it's that there appears to be no difference between statistics that are calculated per 100 team possessions and in the percantage (or portion) of the statistic that that player had while on the floor.

## __FOCUS__: `PTS`, `AST`, `AST%`

- __PTS__: Points Per 100 Team Possessions
- __AST__: Assists Per 100 Team Possessions
- __AST%__: An estimate of the percentage of teammate field goals a player assisted while he was on the floor.


```python
eda_scatterplot(df_per_qualify, 'PTS', 'WS/48', 'Points Per 100 Team Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_69_0.png)



```python
eda_scatterplot(df_per_qualify, 'AST', 'WS/48', 'Assists Per 100 Team Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_70_0.png)



```python
eda_scatterplot(df_per_qualify, 'AST%', 'WS/48', 'Assist % and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_71_0.png)


__OBSERVATION__: The most relevant observation for this section is in regards to `PTS`; it appears that the more points a player scored generally led to a higher `WS/48` value. This is pretty logical in that to win a game, a team has to score more points than the other team! 

What was a little surprising was the scatter plots related to assists. Generally, it is assumed that better teamwork (i.e. which one would assume would equal more assists) equals more wins. But the above graphs indicate a different story. Now this does not mean that we throw assists out the window, as there is [evidence](https://www.ncbi.nlm.nih.gov/pubmed/11361327) that suggests how a team scores is more important the the total they score. 

But there are also other [variables](https://www.sbnation.com/2013/4/10/4208428/nba-assists-shooting-knicks-heat-bulls) at play, like playing style, that affect the number of assists a team averages per game. So while the graphs may be rather ambiguous, we're not going to disregard assists quite yet.

## __FOCUS__: `STL`, `STL%`, `BLK` and `BLK%`

- __STL__: Steals Per 100 Team Possessions
- __STL%__: An estimate of the percentage of opponent possessions that end with a steal by the player while he was on the floor.
- __BLK__: Blocks Per 100 Team Possessions
- __BLK%__: An estimate of the percentage of opponent two-point field goal attempts blocked by the player while he was on the floor.


```python
eda_scatterplot(df_per_qualify, 'STL', 'WS/48', 'Steals Per 100 Team Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_74_0.png)



```python
eda_scatterplot(df_per_qualify, 'STL%', 'WS/48', 'Steal % and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_75_0.png)



```python
eda_scatterplot(df_per_qualify, 'BLK', 'WS/48', 'Blocks Per 100 Team Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_76_0.png)



```python
eda_scatterplot(df_per_qualify, 'BLK%', 'WS/48', '% Of Blocks and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_77_0.png)


__OBSERVATION__: The plots of `STL` and `STL%` essentially don't tell us anything except that steals don't appear to have any significant impact on `WS/48`. 

My hypothesis behind this is that while they do give a team additional possessions to score, their rarity cancels out any positive impact they may have. 

## __FOCUS__: `TOV`, `TOV%`, and `PF`

- __TOV__: Turnovers Per 100 Team Possessions
- __TOV%__: An estimate of turnovers committed per 100 plays.
- __PF__: Personal Fouls Per 100 Team Possessions


```python
eda_scatterplot(df_per_qualify, 'TOV', 'WS/48', 'Turnovers Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_80_0.png)



```python
eda_scatterplot(df_per_qualify, 'TOV%', 'WS/48', '% of Turnovers and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_81_0.png)



```python
eda_scatterplot(df_per_qualify, 'PF', 'WS/48', 'Personal Fouls Per 100 Possessions and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_82_0.png)


__OBSERVATION__: Again, statistics related to percentages making their prescence known! From the scatter plot of turnover %, we can see that there is a downward sloping relationship. In other words, the higher the percentage of tunrovers that a player makes, the lower his `WS/48` score is going to be. 

This is the case of another pretty logical though processes. By turning the ball over, a player is giving the opposing team another possession to score and on average this is going to decrease his teams chances of winning, negatively impacting his `WS/48`. 

Personal fouls may have slightly negative relationship with `WS/48` but that is far from conclusive. This is a little surprising considering that fouling other players (when they are in the process of shooting) lead to free throws, which as mentioned earlier, can be free points. I expected a more discernable relationship between these two variables but I guess this highlights that one should never assume with data!

## __FOCUS__: `ORtg`, `DRtg`, `USG%` and `PER`

- __ORtg__: An estimate of points produced per 100 possessions
- __DRtg__: An estimate of points allowed per 100 possessions
- __USG%__: An estimate of the percentage of team plays used by a player while he was on the floor.
- __PER__: A measure of per-minute production standardized such that the league average is 15.


```python
eda_scatterplot(df_per_qualify, 'ORtg', 'WS/48', 'Offensive Rating and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_85_0.png)



```python
eda_scatterplot(df_per_qualify, 'DRtg', 'WS/48', 'Defensive Rating and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_86_0.png)



```python
eda_scatterplot(df_per_qualify, 'USG%', 'WS/48', 'Usage % and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_87_0.png)



```python
eda_scatterplot(df_per_qualify, 'PER', 'WS/48', 'Player Efficiency Rating and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_88_0.png)


__OBSERVATION__: Looks like we're finally getting to the good stuff! While the scatter plot of `USG%` (which essentially tells us how 'involved' a player is in a team's offense) and `DRtg` were disappointing, the other variables -- `ORtg` and `PER` -- made up for it. 

Now the equation for offenseive rating is pretty intense but you can find it [here](https://www.basketball-reference.com/about/ratings.html) if you're interested. However, I do want to provide some context for what exactly `ORtg` encapsulates, so here is an excerpt from [Basketball-reference.com](https://www.basketball-reference.com/about/ratings.html).

- 'In Dean's[*] words, "Individual offensive rating is the number of points produced by a player per hundred total individual possessions. In other words, 'How many points is a player likely to generate when he tries?'" The basic building blocks of the Offensive Rating calculation are Individual Total Possessions and Individual Points Produced. 

   The formula for Total Possessions is broken down into four components: Scoring Possessions, Missed FG Possessions, Missed FT Possessions, and Turnovers.'
    
    
Essentially `ORtg` is an efficiency metric that explains how effectively a player uses his opportunities (i.e. possessions) during the course of a game. Given the same number of opportunities, a player is going to be more 'efficient' if they contribute to a team scoring more often. 


Now `PER` is similar in that it too is an efficiency metric but combines a player's contributions (or mistakes...) on both the offensive and defensive side of the floor. The following is an excerpt from [Basketball-reference.com](https://www.basketball-reference.com/about/per.html):

- "The Player Efficiency Rating (PER) is a per-minute rating developed by ESPN.com columnist John Hollinger. In John's words, 'The PER sums up all a player's positive accomplishments, subtracts the negative accomplishments, and returns a per-minute rating of a player's performance.'"

Now both of these statistics have a strong positive relationship with `WS/48`, in that the higher a given player's `ORtg` or `WS/48`, the higher that player's `WS/48` is going to be. We'll definitely take note of this for later on when we go further into the machine learning portion of the project.
    


[*] Dean Oliver, who wrote _Basketball on Paper_, developed both the offensive and defensive rating metrics



## __FOCUS__: `OBPM`, `DBPM`, and `BPM`

- __OBPM__: A box score estimate of the offensive points per 100 possessions a player contributed above a league-average player, translated to an average team.
- __DBPM__: A box score estimate of the defensive points per 100 possessions a player contributed above a league-average player, translated to an average team.
- __BPM__: A box score estimate of the points per 100 possessions a player contributed above a league-average player, translated to an average team.


```python
eda_scatterplot(df_per_qualify, 'OBPM', 'WS/48', 'Offensive Box +/- and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_91_0.png)



```python
eda_scatterplot(df_per_qualify, 'DBPM', 'WS/48', 'Defensive Box +/- and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_92_0.png)



```python
eda_scatterplot(df_per_qualify, 'BPM', 'WS/48', 'Overall Box +/- and Win Shares Per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_93_0.png)


__OBSERVATION__: As the name implies, Box +/- is a box score-based metric and combines a player's box score info with a team's overall performace then relates his performace to a league average, which is 0.0. 

For context, the following are the different 'levels' of Box +/- and the types of players that could be included in that level

- +5 BPM: 5 points better than an average player over 100 poseessions (All-NBA)
- 0 BPM: league average (most players are around this level)
- -2 BPM: replacement level (inconsistent player who on average hurts more than he helps a teams chances of winning)
- -5 BPM: bad (i.e. player should not be on the court save for garbage time)

Both `OBPM` and `BPM` are nice looking scatter plots with a pretty distinct (positive) relationship with `WS/48` but I'd say that the more interesting takeaway from this sections has to do with `DBPM`. In the last section, we saw that `DRtg` had a fairly ambiguous relationship with win shares and here we are seeing a similar story with `DBPM`. This is adding evidence to the notion that defense may not be as important in the NBA. How good a player is offensively may in fact be king when it comes to predicting his contribution to a team's chances of winning.


```python
df_per_qualify.sort_values(by = 'PER', ascending=False).head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Player</th>
      <th>Pos</th>
      <th>Age</th>
      <th>Tm</th>
      <th>G</th>
      <th>GS</th>
      <th>MP</th>
      <th>FG</th>
      <th>FGA</th>
      <th>FG%</th>
      <th>3P</th>
      <th>3PA</th>
      <th>3P%</th>
      <th>2P</th>
      <th>2PA</th>
      <th>2P%</th>
      <th>FT</th>
      <th>FTA</th>
      <th>FT%</th>
      <th>ORB</th>
      <th>DRB</th>
      <th>TRB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TOV</th>
      <th>PF</th>
      <th>PTS</th>
      <th>ORtg</th>
      <th>DRtg</th>
      <th>PER</th>
      <th>TS%</th>
      <th>3PAr</th>
      <th>FTr</th>
      <th>ORB%</th>
      <th>DRB%</th>
      <th>TRB%</th>
      <th>AST%</th>
      <th>STL%</th>
      <th>BLK%</th>
      <th>TOV%</th>
      <th>USG%</th>
      <th>OWS</th>
      <th>DWS</th>
      <th>WS</th>
      <th>WS/48</th>
      <th>OBPM</th>
      <th>DBPM</th>
      <th>BPM</th>
      <th>VORP</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4522</th>
      <td>LeBron James</td>
      <td>SF</td>
      <td>24</td>
      <td>CLE</td>
      <td>81</td>
      <td>81</td>
      <td>3054</td>
      <td>14.0</td>
      <td>28.6</td>
      <td>0.489</td>
      <td>2.3</td>
      <td>6.8</td>
      <td>0.344</td>
      <td>11.6</td>
      <td>21.8</td>
      <td>0.535</td>
      <td>10.5</td>
      <td>13.5</td>
      <td>0.780</td>
      <td>1.9</td>
      <td>9.0</td>
      <td>10.9</td>
      <td>10.4</td>
      <td>2.4</td>
      <td>1.6</td>
      <td>4.3</td>
      <td>2.5</td>
      <td>40.8</td>
      <td>122.0</td>
      <td>99.0</td>
      <td>31.7</td>
      <td>0.591</td>
      <td>0.238</td>
      <td>0.472</td>
      <td>4.3</td>
      <td>19.0</td>
      <td>11.9</td>
      <td>38.0</td>
      <td>2.4</td>
      <td>2.4</td>
      <td>11.0</td>
      <td>33.8</td>
      <td>13.7</td>
      <td>6.5</td>
      <td>20.3</td>
      <td>0.318</td>
      <td>9.4</td>
      <td>3.6</td>
      <td>13.0</td>
      <td>11.6</td>
      <td>2009</td>
    </tr>
    <tr>
      <th>2686</th>
      <td>LeBron James</td>
      <td>PF</td>
      <td>28</td>
      <td>MIA</td>
      <td>76</td>
      <td>76</td>
      <td>2877</td>
      <td>14.1</td>
      <td>24.9</td>
      <td>0.565</td>
      <td>1.9</td>
      <td>4.7</td>
      <td>0.406</td>
      <td>12.2</td>
      <td>20.2</td>
      <td>0.602</td>
      <td>7.4</td>
      <td>9.8</td>
      <td>0.753</td>
      <td>1.8</td>
      <td>9.4</td>
      <td>11.2</td>
      <td>10.1</td>
      <td>2.4</td>
      <td>1.2</td>
      <td>4.2</td>
      <td>2.0</td>
      <td>37.5</td>
      <td>125.0</td>
      <td>101.0</td>
      <td>31.6</td>
      <td>0.640</td>
      <td>0.188</td>
      <td>0.395</td>
      <td>4.4</td>
      <td>20.8</td>
      <td>13.1</td>
      <td>36.4</td>
      <td>2.4</td>
      <td>1.9</td>
      <td>12.4</td>
      <td>30.2</td>
      <td>14.6</td>
      <td>4.7</td>
      <td>19.3</td>
      <td>0.322</td>
      <td>9.2</td>
      <td>2.4</td>
      <td>11.6</td>
      <td>9.8</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>1130</th>
      <td>Stephen Curry</td>
      <td>PG</td>
      <td>27</td>
      <td>GSW</td>
      <td>79</td>
      <td>79</td>
      <td>2700</td>
      <td>14.4</td>
      <td>28.6</td>
      <td>0.504</td>
      <td>7.2</td>
      <td>15.9</td>
      <td>0.454</td>
      <td>7.2</td>
      <td>12.7</td>
      <td>0.566</td>
      <td>6.5</td>
      <td>7.2</td>
      <td>0.908</td>
      <td>1.2</td>
      <td>6.5</td>
      <td>7.7</td>
      <td>9.4</td>
      <td>3.0</td>
      <td>0.3</td>
      <td>4.7</td>
      <td>2.9</td>
      <td>42.5</td>
      <td>125.0</td>
      <td>103.0</td>
      <td>31.5</td>
      <td>0.669</td>
      <td>0.554</td>
      <td>0.250</td>
      <td>2.9</td>
      <td>13.6</td>
      <td>8.6</td>
      <td>33.7</td>
      <td>3.0</td>
      <td>0.4</td>
      <td>12.9</td>
      <td>32.6</td>
      <td>13.8</td>
      <td>4.1</td>
      <td>17.9</td>
      <td>0.318</td>
      <td>12.4</td>
      <td>0.1</td>
      <td>12.5</td>
      <td>9.8</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>3927</th>
      <td>Ryan Bowen</td>
      <td>SF</td>
      <td>34</td>
      <td>OKC</td>
      <td>1</td>
      <td>0</td>
      <td>8</td>
      <td>6.4</td>
      <td>6.4</td>
      <td>1.000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000</td>
      <td>6.4</td>
      <td>6.4</td>
      <td>1.000</td>
      <td>12.9</td>
      <td>12.9</td>
      <td>1.000</td>
      <td>6.4</td>
      <td>6.4</td>
      <td>12.9</td>
      <td>0.0</td>
      <td>6.4</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12.9</td>
      <td>25.8</td>
      <td>224.0</td>
      <td>96.0</td>
      <td>31.3</td>
      <td>1.064</td>
      <td>0.000</td>
      <td>2.000</td>
      <td>14.7</td>
      <td>14.0</td>
      <td>14.3</td>
      <td>0.0</td>
      <td>6.4</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>10.5</td>
      <td>0.1</td>
      <td>0.0</td>
      <td>0.1</td>
      <td>0.509</td>
      <td>8.3</td>
      <td>-0.2</td>
      <td>8.1</td>
      <td>0.0</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>4094</th>
      <td>LeBron James</td>
      <td>SF</td>
      <td>25</td>
      <td>CLE</td>
      <td>76</td>
      <td>76</td>
      <td>2966</td>
      <td>13.6</td>
      <td>27.1</td>
      <td>0.503</td>
      <td>2.3</td>
      <td>6.9</td>
      <td>0.333</td>
      <td>11.3</td>
      <td>20.2</td>
      <td>0.560</td>
      <td>10.5</td>
      <td>13.7</td>
      <td>0.767</td>
      <td>1.3</td>
      <td>8.6</td>
      <td>9.8</td>
      <td>11.5</td>
      <td>2.2</td>
      <td>1.4</td>
      <td>4.6</td>
      <td>2.1</td>
      <td>40.0</td>
      <td>121.0</td>
      <td>102.0</td>
      <td>31.1</td>
      <td>0.604</td>
      <td>0.253</td>
      <td>0.506</td>
      <td>3.0</td>
      <td>18.5</td>
      <td>11.1</td>
      <td>41.8</td>
      <td>2.2</td>
      <td>2.0</td>
      <td>12.3</td>
      <td>33.5</td>
      <td>13.3</td>
      <td>5.2</td>
      <td>18.5</td>
      <td>0.299</td>
      <td>9.7</td>
      <td>2.8</td>
      <td>12.5</td>
      <td>10.9</td>
      <td>2010</td>
    </tr>
  </tbody>
</table>
</div>



## Statistical Minimums to Qualify For NBA League Leaders

As you can see from the sorted values in the `PER` qualified data set, there is a player named `Ryan Bowen` who technically qualified under our `PER` threshold of 6.09 minutes per game. Specifically, Bowen played in one game for a total of 8 minutes. However, this is somewhat misleading. 

So it looks like using the 6.09 minutes/game threshold for `PER` may not be the best direction which brings me to the subject of statistical minimums. To qualify, the NBA has set a certain threshold when it comes tracking statistics. Below are the [minimum threshold](https://stats.nba.com/help/statminimums/) for each statistic that players must meet to be included amongest 'league leaders'.

- SCORING: Player must play 70% of his team's games (58 in 82-game season)
- REBOUNDS: Player must play 70% of his team's games (58 in 82-game season)
- FIELD GOAL %: 300 field goals made
- FREE THROW %: 125 free throws made
- 3PT %: 82 three-point field goals made
- ASSISTS: Player must play 70% of his team's games (58 in 82-game season)
- STEALS: Player must play 70% of his team's games (58 in 82-game season)
- BLOCKED SHOTS: Player must play 70% of his team's games (58 in 82-game season)
- MINUTES: Player must play 70% of his team's games (58 in 82-game season)

With this information, we're going to pivot slightly and take a look at what the data looks like for players that played in 70% of their respective team's games.


```python
# create DataFrame that only includes players that meet the minimum 58 game threshold
df_threshold = df[df['G'] >= 58]

print('There are', df_threshold.shape[0], 'observations that meet the minimum 58-game threshold.')
print('This is in comparison to', df.shape[0], 'in the original data set.')
print('Thats a difference of', abs(df_threshold.shape[0] - df.shape[0]), 'observations! Or,',
      (abs(df_threshold.shape[0] - df.shape[0])) / df.shape[0] * 100, '% of observations.')
```

    There are 2526 observations that meet the minimum 58-game threshold.
    This is in comparison to 4759 in the original data set.
    Thats a difference of 2233 observations! Or, 46.92162218953562 % of observations.



```python
# export threshold DataFrame
df_threshold.to_csv('Data/df_threshold.csv')
```

Woah, that escalated quickly! While I don't think we want to eliminate that many observations, we'll explore this data set further because it focuses primarily on the players that can potentially give us greater insights because of the simple fact that play in games and they play often!


```python
# plot PER and TS%
f, ax = plt.subplots(figsize=(10, 6))
sns.regplot(x="PER", y="WS/48", data=df_threshold, 
            ax=ax, scatter_kws={'alpha':0.5, 'color':'steelblue'}, line_kws={"color": "orange"});
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_100_0.png)


__OBSERVATION__: This looks a lot better as there does not appear to be any significant outliers. While I'm not too keen on losing close to half of a data sets observations, I want to dive a little deeper to see what trends we get with the threshold data set.

## Focus: The Threshold `WS/48` 


```python
# scatter plot of WS/48 and MP
eda_scatterplot(df_threshold, 'MP', 'WS/48', 'Minutes Played and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_103_0.png)



```python
# scatter plot of WS/48 and FG
eda_scatterplot(df_threshold, 'FG', 'WS/48', 'Field Goals and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_104_0.png)



```python
# scatter plot of WS/48 and FG%
eda_scatterplot(df_threshold, 'FG%', 'WS/48', 'Field Goal % and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_105_0.png)



```python
# scatter plot of WS/48 and TS%
eda_scatterplot(df_threshold, 'TS%', 'WS/48', 'Field Goal % and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_106_0.png)



```python
# scatter plot of WS/48 and TS%
eda_scatterplot(df_threshold, 'AST', 'WS/48', 'Assists and Win Shares per 48 Minutes')
```


![png](Exploratory-Data-Analysis-WinShares_files/Exploratory-Data-Analysis-WinShares_107_0.png)

