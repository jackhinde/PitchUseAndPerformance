# Overview

This is a project using Python, where LASSO regression is fit to model the relationship between changes in use of a pitch type and its change in performance using Statcast data between the 2021 and 2023 MLB seasons. The project can be found in the file [*PitchUseAndPerformance.ipynb*](https://github.com/jackhinde/PitchUseAndPerformance/blob/main/PitchUseAndPerformance.ipynb).

This project was presented at the 2024 Saberseminar. A link to the presentation can be found [here](https://youtu.be/_AcaHNSP3RU).

# Pitch Use and Performance

The use of technology and data for improving pitchers' performance has been hugely successful. With many measurements from every pitch thrown in an MLB game widely available, there have been massive advancements in quantifying the effect of these to the success of a pitch and providing data-driven feedback for pitchers to ensure that every time they deliver a pitch they're throwing the best version of the pitch that they can.

With all of the advancements that the data has provided for understanding whether pitchers are throwing each pitch well, we should also be focusing on producing quantitative answers as to whether they are using each of their pitch types well. However in order to quantify whether pitchers are using their pitch types optimally, an understanding of how the pitch type will be affected by changing its use is required.

The motivation behind this project is to apply the assumption that the performance of a pitch type is dependent on how often the pitcher throws it, to model this effect, and to predict this for any of a pitcher's pitch types. Performance of a pitch type will be measured in [Run Value per 100 Pitches](https://library.fangraphs.com/pitching/linear-weights/).

Previous research has been devoted to this topic, and there are two excellent works of research that influenced this project. In Carmen Ciardiello's article [Does Throwing a Pitch More Alter its Effectiveness](https://blogs.fangraphs.com/does-throwing-a-pitch-more-alter-its-effectiveness/), year-over-year changes in percentage thrown and performance by Swinging Strike Rate and Run Value per 100 Pitches were compared. This comparison was also done by pitch type and in intervals of percentage thrown. At the 2019 Saberseminar Vicente Iglesias presented [Game Theory Analysis of Optimal Pitch Frequency Mix](https://www.youtube.com/watch?v=V3dg5PQIqyA) where a regression model was fit to predict performance of pitch types in Run Value per 100 Pitches in categories of fastball and offspeed pitches. This regression model was able to find a percentage thrown for each pitcher's pitches that provides an optimal Run Value.

In making a prediction for the performance of any pitch type, fundamental differences in their prior performance and use must be considered. We measure and predict changes in performance and use against these prior values to allow changes to affect what's observed of them. To model throwing a pitch type more or less often we measure with how much more or less often they threw it than it's average use. Symbolically this is $\Delta x = x - \mu$, where $x$ is the percentage use and $\mu$ is the pitcher's average use of the pitch type. The assumption that the use of a pitch type affects its performance can be expressed as $RV(\Delta RV(\Delta x)) = RV + \Delta RV(\Delta x)$, where $RV$ is the Run Value per 100 Pitches of the pitch type and $\Delta RV(\Delta x)$ is the difference in Run Value per 100 Pitches influenced by the change in how often the pitcher threw the pitch type.

The changes to performance of a pitch type will also be measured against the observed performance of a pitch type. To compare each pitch type to its typical level of performance, samples of 1000 consecutive pitches from a pitcher will be created. The proportion of time that the pitcher threw the pitch type and the Run Value per 100 Pitches of the pitch type in the sample will be calculated, and these differences will be calculated as $\Delta x = \hat{x} - \mu$ and $\Delta RV(\Delta x) = \hat{RV} - RV$ where $\hat{x}$ and $\hat{RV}$ are the proportion of use and the performance in the sample. The data is split by season within the data, so that samples are compared to the average use and performance of the pitcher's pitch type that season.

The goal of this project will be fitting a function $f$ to approximate the expected change in performance of the pitch type as a consequence of changing its use, that is $\Delta RV(\Delta x) = \hat{RV} - RV \approx f(\Delta x)$. The data used in fitting this model and in making predictions is from MLB's publicly-available [Statcast database](https://baseballsavant.mlb.com/statcast_search) which is scraped using the [pybaseball](https://pypi.org/project/pybaseball/) package. The dataset includes every pitch thrown in an MLB regular season game between 2021 and 2023. More details about the data can be found in the [Statcast Search CSV Documentation](https://baseballsavant.mlb.com/csv-docs).

Other variables that may influence the change in performance of the pitch type will be considered. These include:
1. The classified pitch type from Statcast
2. The rank of the pitch type in the pitcher's mix (most thrown, second-most thrown, etc.)
3. Pitch-level metrics from Statcast. These are:\
a. The velocity of the pitch\
b. The spin rate of the pitch\
c. The spin axis of the pitch\
d. The extension of the pitcher's release towards home plate on the pitch\
e. The pitcher's horizontal and vertical release on the pitch\
f. The horizontal and vertical break of the pitch

Interactions between each of those variables will also be included in the model.

The method that will be used to fit the model will be [LASSO regression](https://en.wikipedia.org/wiki/Lasso_(statistics)). This method was chosen as it accomplishes the goal of modeling and predicting the change in performance, and it selects the subset of variables that best explain the variation in the data under the assumption that not all of the variables are relevant to this.

The file [*PitchUseAndPerformance.ipynb*](https://github.com/jackhinde/PitchUseAndPerformance/blob/main/PitchUseAndPerformance.ipynb) loads, cleans, and formats the data, and fits the model. The results of the model are calculated in the notebook, and the model is applied in three examples. The first example is in predicting the average expected change by pitch type at two different levels of increase to percentage thrown, and the second and third examples make predictions using the pitch types of Gerrit Cole and Blake Snell in 2023.

The notebook can be run to produce the model, and predictions of changes to performance as their use is changed can be made on the pitch types of any pitcher who appeared between 2021 and 2023 using the `view_pred_RV_per_100_diff` function.

# References

Carmen Ciardiello, "Does Throwing a Pitch More Alter Its Effectiveness?," *Fangraphs Baseball*, April 27, 2021. <https://blogs.fangraphs.com/does-throwing-a-pitch-more-alter-its-effectiveness/>

Vicente Iglesias, "Game Theory Analysis of Optimal Pitch Frequency Mix," Conference Presentation, *Saberseminar 2019, Boston, MA, United States*, August 17-18, 2019. <https://www.youtube.com/watch?v=V3dg5PQIqyA>

James LeDoux and Moshe Schorr, "playerid_lookup," *pybaseball*, (2017), GitHub repository. <https://github.com/jldbc/pybaseball/blob/master/docs/playerid_lookup.md>

James LeDoux and Moshe Schorr, "pybaseball 2.2.7," *PyPI*, September 8, 2023. <https://pypi.org/project/pybaseball/>

James LeDoux and Moshe Schorr, "statcast," *pybaseball*, (2017), GitHub repository. <https://github.com/jldbc/pybaseball/blob/master/docs/statcast.md>

Piper Slowinski, "Pitch Type Linear Weights \| Sabermetrics Library," *Fangraphs Baseball*, February 18, 2010. <https://library.fangraphs.com/pitching/linear-weights/>

"Statcast Search," *Baseball Savant*. <https://baseballsavant.mlb.com/statcast_search>

"Statcast Search CSV Documentation," *Baseball Savant*. <https://baseballsavant.mlb.com/csv-docs>

"Lasso (statistics)," *Wikipedia*, June 19, 2024. [https://en.wikipedia.org/wiki/Lasso\_(statistics)](https://en.wikipedia.org/wiki/Lasso_(statistics)){.uri}
