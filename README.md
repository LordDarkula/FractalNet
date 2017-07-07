# Predicting fractal Dimension using Machine Learning
## Motivation
Fractal Dimension is a way to measure the complexity of a fractal. This project's purpose it too estimate it with machine learning. Traditional methods such as box counting have shortcomings, such as a lack of versatility with many different types of fractals, and inefficient compute times. Machine learning was used to give good approximations of the fractal dimension of any fractal with reasonable speed.
## Data Collection
I decided to use an image of a fractal as the input, and the dimension as the output. This allowed the algorithm to classify many types of fractals.
### Wikipedia
Wikipedia has a set of fractal images, as well the corresponding fractal dimensions. I looked through them to pick the ones with high quality images for my training set, however it was not enough.
### Randomly Generated Julia Sets
I chose to generate random Julia Sets, as there is a built in function for it in Wolfram Language. Julia Sets have a single parameter called c, and changing it generates unique fractals. I generated Julia Sets with random c values, both real and complex, to give the sets some variety.

    CreateJuliaFractals[size_] :=
        Module[{jFunction = {JuliaSetPlot[#], #} &, jFractals},
        jFractals =
        jFunction /@ Table[RandomComplex[], Floor[size/2]] \[Union]
        jFunction /@ Table[RandomReal[{-1, 1}], Ceiling[size/2]];
        Association[#[[1]] -> JuliaFractalDimension[#[[2]]] & /@ jFractals]
    ]

Calculating the fractal dimension from a given c value of a Julia Set is relatively simple.

    JuliaFractalDimension[c_] := 1 + Abs[c]^2/4 Log[2]

## Training
I split the data into train and test and used the Predict function in Wolfram Language, as it has the capability of returning a result that differs from the training set.

    FractalDimensionPredictor[training_] := Predict[List @@ training]

## Evaluation
I returned the real and predicted fractal dimension as well as the margin of error for every fractal in the test set.

    FractalMapEval[pred_, test_] := Keys[KeyMap[# -> {test[#], pred[#]} &, test]]

## Deployment
I created a micro site in which people can put in pictures of any fractal and get an approximation of it's dimension.

    CloudDeploy[FormFunction[{"Fractal" -> "Image"},
        Column[{#image, pred[#image]}] &, "PNG",
        AppearanceRules -> <|"Title" ->     "Predict Fractal Dimension"|>],
        Permissions -> "Public"]

## Conclusion
Before doing this project, I had no idea what fractal dimension was, and I learned many things including data generation and continuous classification. In the future I hope to use iterated function system to generate more fractals.

Visit the micro site [here](https://www.wolframcloud.com/objects/7102eabb-7b80-4bb1-9652-eebcd75e90cd).

Link to community post [here](http://community.wolfram.com/groups/-/m/t/1140551).
