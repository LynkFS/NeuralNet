# NeuralNet
Object Pascal feed forward neural network component

Simple 3 layer feed-forward neural network component\
\
No dependencies\
Can be used in conjunction with DWS compilers\
\
demo1: https://www.lynkit.com.au/neuralnet/mnist/mnist.html <br>
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
    writeln('NeuralNet started');

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
          var data: JNeuralNetworkTrainData;
          data.input := [x, y];
          if (x xor y) = 0 then
            data.output := [0.99, 0.01] else
            data.output := [0.01, 0.99];
          MyNetwork.AddTrainingData(data.input, data.output);
        end;

  // train
    MyNetwork.LearningRate := 0.2;
    MyNetwork.TrainingSplit := 10;       //split trainingset randomly: 10% for testing, 90% training

    For var i := 1 to 5 do begin      //every iteration is an epoch
      //console.log('Epoch ' + inttostr(i) + '/5');
      MyNetwork.Train;
    end;

  // test
    MyNetwork.Test;

    for var q := 0 to 10 do begin  //MyNetwork.ResultA.NrRows -1 do begin  //for all test records
      var a := inttostr(GetMaxIndex(q, MyNetwork.ResultB));     //correct outcome
      var b := inttostr(GetMaxIndex(q, MyNetwork.ResultC));     //computed outcome

      writeln(MyNetwork.ResultA.GetValue(q,0) + ' XOR ' +
              MyNetwork.ResultA.GetValue(q,1) + ': ' +
              MyNetwork.ResultC.GetValue(q,1) + ' - should be ' + a);
    end;
  end;
  ```
output\
\
NeuralNet started\
0 XOR 0: 0.05773579608792857 - should be 0\
1 XOR 0: 0.9403986284540853 - should be 1\
0 XOR 1: 0.9524118223319257 - should be 1\
1 XOR 1: 0.05424365311145005 - should be 0
