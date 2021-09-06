## Replication Package For Paper: Learning CNN-Encoded Propositions for Robotic Programming from Few Demonstrations

### Code
We refactor the code of this project, which could be found on this.
---
### data
The data is access here: [traj_capability](https://1drv.ms/u/s!AtQlfXL28GxeajG8PychjufKca8?e=bMah0V) and [traj_demonstration](https://1drv.ms/u/s!An_sqEOHEaVaalbqMgGmadqPvqI?e=6wKFlF)
---
### capNet 

todo
---
### baseNet and propNet

todo
---

### Evaluations

1. RQ1: General Accuracy
<table>
    <tr>
        <td></td>
        <td></td>
        <td>cnt</td>
        <td>#TP</td>
        <td>#TN </td>
        <td>#FP</td>
        <td>#FN</td>
        <td>Accuracy</td>
        <td>#ES</td>
        <td>#EF</td>
    </tr>
    <tr>
        <td rowspan="2">Our method</td>
        <td>SeR</td>
        <td>152</td>
        <td>130</td>
        <td>8</td>
        <td>8</td>
        <td>6</td>
        <td>90.80%</td>
        <td>28</td>
        <td>8</td>
    </tr>
    <tr>
        <td>VeR</td>
        <td>151</td>
        <td>120</td>
        <td>11</td>
        <td>15</td>
        <td>5</td>
        <td>86.80%</td>
        <td>19</td>
        <td>11</td>
    </tr>
    <tr>
        <td rowspan="2">Baseline</td>
        <td>SeR</td>
        <td>184</td>
        <td>166</td>
        <td>14</td>
        <td>4</td>
        <td>0</td>
        <td>97.80%</td>
        <td>32</td>
        <td>14</td>
    </tr>
    <tr> 
        <td>VeR</td>
        <td>73</td>
        <td>25</td>
        <td>0</td>
        <td>48</td>
        <td>0</td>
        <td>34.20%</td>
        <td>2</td>
        <td>0</td>
    </tr>
</table>

2. RQ2: Impact of the Number of Demonstrations
<table>
    <tr>
        <td>#demonstration</td>
        <td>cnt</td>
        <td>#TP</td>
        <td>#TN </td>
        <td>#FP</td>
        <td>#FN</td>
        <td>Accuracy</td>
        <td>#ES</td>
        <td>#EF</td>
    </tr>
    <tr>
        <td>1</td>
        <td>140</td>
        <td>100</td>
        <td>13</td>
        <td>19</td>
        <td>8</td>
        <td>80.70%</td>
        <td>10</td>
        <td>13</td>
    </tr>
    <tr>
        <td>5</td>
        <td>152</td>
        <td>130</td>
        <td>8</td>
        <td>8</td>
        <td>6</td>
        <td>90.80%</td>
        <td>28</td>
        <td>8</td>
    </tr>
    <tr>
        <td>10</td>
        <td>169</td>
        <td>148</td>
        <td>11</td>
        <td>10</td>
        <td>0</td>
        <td>94.10%</td>
        <td>29</td>
        <td>11</td>
    </tr>
    <tr>
        <td>20</td>
        <td>159</td>
        <td>136</td>
        <td>15</td>
        <td>4</td>
        <td>4</td>
        <td>95.00%</td>
        <td>27</td>
        <td>15</td>
    </tr>
</table>

3. RQ3: Impact of the Demonstrations under Difference Environment Settings

<table>
    <tr>
        <td>#demonstration</td>
        <td>cnt</td>
        <td>#TP</td>
        <td>#TN </td>
        <td>#FP</td>
        <td>#FN</td>
        <td>Accuracy</td>
        <td>#ES</td>
        <td>#EF</td>
    </tr>
    <tr>
        <td>10</td>
        <td>162</td>
        <td>134</td>
        <td>11</td>
        <td>8</td>
        <td>9</td>
        <td>89.50%</td>
        <td>22</td>
        <td>11</td>
    </tr>
</table>

---
#### Video
todo
