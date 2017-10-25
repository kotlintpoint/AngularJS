- Jasmine is an open source testing framework for the JavaScript language developed by Pivotal Labs. 
- Its syntax is pretty similar to one of the most famous testing frameworks, RSpec.
- you just need to download it from GitHub at http://jasmine.github.io and unzip it. 
- Copy lib/jamine-core to your project and go through following code

###### parkingFactoryFunction.js
```
var parkingFactoryFunction = function () {
  var _calculateTicket = function (car) {
    //var departHour = new Date().getHours();
    var departHour = car.depart.getHours();
    var entranceHour = car.entrance.getHours();
    var parkingPeriod = departHour - entranceHour;
    var parkingPrice = parkingPeriod * 10;
    return {
      period: parkingPeriod,
      price: parkingPrice
    };
  };
  return {
    calculateTicket: _calculateTicket
  };
};
```
###### parkingFactoryFunctionSpec.js
```
describe("Parking Factory Function Specification", function () { 
	it("Should calculate the ticket for a car that arrives any day at 08:00 and departs in the same day at 16:00", function () {         
	var car = {place: "AAA9988", color: "Blue"};
	car.entrance = new Date(new Date().getMilliseconds());
    car.depart = new Date(new Date().getMilliseconds()+28800000);
    var parkingService = parkingFactoryFunction();
    var ticket = parkingService.calculateTicket(car);
    expect(ticket.period).toBe(8);
    expect(ticket.price).toBe(80);
  }); 
});
```
###### SpecRunner.html
```
<!DOCTYPE HTML>
<html>
  <head>
    <title>Jasmine Spec Runner v2.0.0</title>
    <link rel="stylesheet" type="text/css" href="jasmine-core/
jasmine.css">
    <script src="jasmine-core/jasmine.js"></script>
    <script src="jasmine-core/jasmine-html.js"></script>
    <script src="jasmine-core/boot.js"></script>
    <script src="Scripts/parkingFactoryFunction.js"></script>
    <script src="Scripts/parkingFactoryFunctionSpec.js"></script>    
  </head>
  <body>
  </body>
</html>
```
