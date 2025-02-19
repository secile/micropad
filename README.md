# remotely control your micro:bit from smartphone via BLE (Bluetooth Low Energy).

Download 'index.html' to your smartphone and open it,  
or access URL https://secile.github.io/micropad/

You can see screen below.
- Press '🚩' button to connect your micro:bit.
- Press '🛑' button to disconnect.

![image](https://github.com/user-attachments/assets/0cbb2cc2-be83-4312-882f-1917065aac9d)

# Control.
There are 4 kinds of control.

1. Analog Stick

![AnalogStick](https://github.com/user-attachments/assets/c1fad99e-c844-4899-8265-e20e59c3212c)

2. Button

button has two mode, normal mode and toggle mode.  
in normal mode, press to turn on and release to turn off.
in toggle mode, switch on and off on every click.

![image](https://github.com/user-attachments/assets/be318136-3676-4d92-816f-4f03a7052343)

3. Slider

![Slider](https://github.com/user-attachments/assets/3873f6b0-f230-4080-9676-c25ebb8b722d)

4. Label

This is only to show text message.

![image](https://github.com/user-attachments/assets/15c9e27b-d02b-4f83-a02e-68249d6122c6)


# transfer message format.
On control updated, micropad send message to micro:bit via BLE. Message format is csv with 3 params 'ControlID, Value1, Value2'.  
ControlID is unique ID every control has it own. Value1 and Value2 is up to control kind.

- Analog Stick
    - ControlID: 'a1'(left stick) or 'a2'(right stick)
    - Value1: radian angle clockwise from the X axis. (rounded in 8 directions)
    - Value2: power. distance from origin. range 0.0-1.0. (rounded in 3 levels, 0.0 or 0.5 or 1.0.)
    - e.g. 'a1, 3.14, 0.5'
- Button
    - ControlID: 'b1'(X Button) or 'b2'(Y Button)
    - Value1: '1'(on) or '0'(off).
    - Value2: '0'(reserved).
    - e.g. 'b2, 1, 0'
- Slider
    - ControlID: 's1'
    - Value1: ratio with 0.0-1.0. (0.1 step)
    - Value2: '0'(reserved).
    - e.g. 's1, 0.7, 0'

## about angle and power
angle is radian angle clockwise from the X axis. power is distance from the origin.  

<img src="https://github.com/user-attachments/assets/a59a97bc-c894-462d-9c60-d61ae9830750" width="35%" />

you can deriver x and y from angle and power.

```js
const x = Math.cos(angle) * power;
const y = Math.sin(angle) * power;
```

# coding on micro:bit.

See my micro:bit MakeCode sample project.  
https://makecode.microbit.org/S93757-34929-39231-95436

- on start
    - add 'bluetooth uart service' to 'on start' 

![image](https://github.com/user-attachments/assets/d6816157-f9f9-4031-bf71-1939c16a0ae1)

- bluetooth on data received
    - receive message from micropad, parse it to 3 params, ControlID, Value1, Value2.
    - then call 'received' function which you make.

![image](https://github.com/user-attachments/assets/954031ad-1f3d-401f-b529-a59a6fff204a)

- received function
    - this is function that does you want.
    - branch by ControlID, interact actions by using Value1 and Value2.
    - here is a sample. Hero moves by Analog Stick, LED shows 'X' or 'Y' by pressing Button, and LED brightness can be changed by Slider. don't forget to add 'create sprite' in 'on start'.

![image](https://github.com/user-attachments/assets/4a72074c-9bdd-4ad8-ae9c-c55ddbc96372)

- of course, don't forget to enable 'No Pairing Required'.

![image](https://github.com/user-attachments/assets/60c656ef-ff0d-468b-bbed-b63cd59a742c)

# customise layout of control.
modify initControl function.

for example, in case you add 3rd button 'Z', follow the instruction below.

- const button3 = new Button(...), and result.push(button3).
- onChange, asign unique Control ID. rename 'b2' to 'b3'.
- on response to 'resize' event, you have to layout it to appropriate position.

```js
// Button 3.
const button3 = new Button(canvas, context);
button3.text = "Z";
button3.onChange = (flag) => {
    const cmd = `b3,${flag? 1: 0},0`;
    sendCmd(cmd)
};
result.push(button3);
window.addEventListener('resize', () => {
    button3.setPosition(canvas.width * 0.5, canvas.height / 2 + stick2.height * 0.75);
});
```

- you can modify control style.

```js
button3.backFillStyle = "LightGreen";
button3.backStrokeStyle = "DarkGreen";
```

![image](https://github.com/user-attachments/assets/b56faa47-b4e5-4fcc-aaee-3d353c7618c4)

