# NeuralNet
Object Pascal feed forward neural network component

Simple 3 layer feed-forward neural network component\
\
No dependencies\
Can be used in conjunction with DWS compilers\
\
demo1: https://www.lynkit.com.au/neuralnet/mnist/index.html <br>
(compiled in QTX-IDE)
\
this demonstrates handwritten character recognition, based on the MNIST dataset\
The net is trained with only 1000 examples to limit training time\
(less than 30 sec on desktop, approx 4 min on mobile)\
which means it is not perfect.\
\
demo2: simple xor
```
  Button.OnClick := procedure(sender:TObject)
  begin
    console.log('started');

  //Init neural network
    var MyNetwork : JW3NeuralNet := JW3NeuralNet.Create;
    //
    MyNetwork.AddLayer  (2, 'Input',  '');            // input layer. input layers have no activation function
    MyNetwork.AddLayer  (5, 'Hidden', 'Sigmoid');     // first hidden layer, with sigmoid activation
    MyNetwork.AddLayer  (2, 'Output', 'Sigmoid');     // output layer, with sigmoid activation
    //
    MyNetwork.SeedNetwork;   //initialise with random values

    // provide some input (XOR function)
    for var iter := 0 to 1000 do
      for var x := 0 to 1 do
        for var y := 0 to 1 do
        begin
          var data: JW3TrainingRecord;
          data.&inputs := [x, y];
          if (x=0) and (y=0) then data.&outputs := [1.0,0.0];
          if (x=0) and (y=1) then data.&outputs := [0.0,1.0];
          if (x=1) and (y=0) then data.&outputs := [0.0,1.0];
          if (x=1) and (y=1) then data.&outputs := [1.0,0.0];
          MyNetwork.AddTrainingData(data.&inputs, data.&outputs);
        end;

  // train
    MyNetwork.LearningRate := 0.2;
    MyNetwork.TrainingSplit := 10;       //split trainingset randomly: 10% for testing, 90% training

    For var i := 1 to 5 do begin      //every iteration is an epoch
      //console.log('Epoch ' + inttostr(i) + '/5');
      MyNetwork.Train;
    end;

  // test the 4 cases
    console.log('0 xor 0 : ' + floattostr(MyNetwork.Test([0,0])) + ' - should be 0');
    console.log('0 xor 1 : ' + floattostr(MyNetwork.Test([0,1])) + ' - should be 1');
    console.log('1 xor 0 : ' + floattostr(MyNetwork.Test([1,0])) + ' - should be 1');
    console.log('1 xor 1 : ' + floattostr(MyNetwork.Test([1,1])) + ' - should be 0');
  end;
  ```
output\
\
NeuralNet started\
0 XOR 0: 0 - should be 0\
1 XOR 0: 1 - should be 1\
0 XOR 1: 1 - should be 1\
1 XOR 1: 0 - should be 0
