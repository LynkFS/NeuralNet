# NeuralNet
Object Pascal feed forward neural network component

Simple 3 layer feed-forward neural network component\
\
No dependencies\
Can be used in conjunction with DWS compilers\
\
<b>demo1</b>: https://www.lynkit.com.au/neuralnet/mnist/index.html <br>
(compiled in QTX-IDE)
\
this demonstrates handwritten character recognition, based on the MNIST dataset\
The net is trained with only 1000 examples to limit training time\
(less than 30 sec on desktop, approx 4 min on mobile)\
which means it is not perfect.\
\
<b>demo2</b>: simple xor
```
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

  ```
output\
\
NeuralNet started\
0 XOR 0: 0 - should be 0\
1 XOR 0: 1 - should be 1\
0 XOR 1: 1 - should be 1\
1 XOR 1: 0 - should be 0\
\
\
<b>demo3</b>: is it a bird, is it a plane, is it a rocket (input : wings, engine)
```
//Init neural network
  MyNetwork := JW3NeuralNet.Create;
//
  MyNetwork.AddLayer  (2, 'Input',  '');           // input layer. input layers have no activation function
  MyNetwork.AddLayer  (9, 'Hidden', 'Sigmoid');    // first hidden layer, with sigmoid activation
  MyNetwork.AddLayer  (3, 'Output', 'Sigmoid');    // output layer, with sigmoid activation
//
  MyNetwork.SeedNetwork;   //initialise with random values
//
  //feed some examples. input: wings,engine  output: bird,plane,rocket
  for var iter := 1 to 10 do  //just 10 examples
    for var x := 0 to 1 do
      for var y := 0 to 1 do
      begin
        var data: JW3TrainingRecord;
        data.&inputs := [x, y];
        if (x=1) and (y=0) then data.&outputs := [1.0,0.0,0.0];    //wings no engine : bird
        if (x=1) and (y=1) then data.&outputs := [0.0,1.0,0.0];    //both wings and engine : plane
        if (x=0) and (y=1) then data.&outputs := [0.0,0.0,1.0];    //no wings, engine : rocket
        if (x=0) and (y=0) then                                    //nothing : do nothing
          else MyNetwork.AddTrainingData(data.&inputs, data.&outputs);
      end;
//
  //train network
  MyNetwork.LearningRate := 0.2;      //MyNetwork.TrainingSplit not necessary
  For var i := 0 to 5 do              //5 epochs
    MyNetwork.Train;
//
  //test
  Button1.OnClick := procedure(sender: TObject)
  begin
    var i : integer := 0;
    var j : integer := 0;
    if CheckBox1.Checked Then i := 1;
    if CheckBox2.Checked then j := 1;
    if (i = 0) and (j = 0)
    then Panel1.SetinnerHTML('unknown')
    else begin
      //run
      var predictedIndex := MyNetwork.Test([i,j]);
      case predictedIndex of
        0 : Panel1.SetinnerHTML('bird');
        1 : Panel1.SetinnerHTML('plane');
        2 : Panel1.SetinnerHTML('rocket');
      end;
    end;
  end;
  ```
