#lld #system-design #design-patterns #java 
# Definition
The Command Pattern encapsulates a request as an object, thereby letting you parameterize other objects with different requests, queue or log requests, and support
undoable operations.
# Need for Command Pattern
Suppose you are working on a home automation system that allows you to control different devices in your house like lights, fan, faucet, garage door etc. Your task is to program a remote control with 7 programmable slots (one slot per device). 
	Each slot has a on and off button associated with it
	There is a global undo button that undoes the last action performed.
Since the slots are dynamic, we must ensure that the correct on/off method is called. 
## A Naive implementation
```java
public interface Device {
	public void on();
	public void off();
}

public class Light extends Device {
	public void on() {
		//some code
	}
	
	public void off() {
		//some code
	}
}

public class GarageDoor extends Device {
	public void on() { //should be up()
		// some code
	}
	
	public void off() { // should be down()
		// some code
	}
}

public class Slot {
	Device device;
	...
}

public class RemoteControl {
	List<Slot> slots;
	
	public void RemoteControl() {
		slots = new ArrayList<>(7);
		for(int i=0; i<7; i++) {
			slots.add(new Slot());
		}
	}
	
	public void addToSlot(Device device, int slotNum) {
		if(0 <= slotNum && slotNum < slots.size())
			slots.set(slotNum, new Slot(device));
	}
	
	public void removeFromSlot(int slotNum) {
		if(0 <= slotNum && slotNum < slots.size()) {
			slots.set(slotNum, new Slot());
		}
	}
	
	public void on(int slotNum) {
		Device device = slots.get(slotNum).getDevice();
		device.on();
	}
	
	public void off(int slotNum) {
		Device device = slots.get(slotNum).getDevice();
		device.off();
	}
	
}
```

Some problems with this approach:
+ What happens if in the future we want to support more functions for a device, like increasing/decreasing speed (for a fan) or increasing/decreasing volume for tv ? 
		We end up polluting the `Device` interface with all these functions.
		Devices like `Fan` will inadvertently support functionality like increasing volume.
+ What happens if want to support automation of multiple instances of a device (ex: control bedroom lights, control kitchen lights).
		We would need separate `Device` subclasses to distinguish between them. Say `BedroomLight` and `KitchenLight`, even though the on/off functionality is the same.
		This can be mitigated by abstracting out common functionality to a new parent class `Light` , which `BedroomLight` and `KitchenLight` extend.
+ What happens if we want to support more devices in the future?
		`Device` interface could be polluted with new functions like increasePower (for a vacuum cleaner).
This violates [](SOLID.md#**O**pen%20for%20extension,%20closed%20for%20modification|Open/closed%20principle) and [](SOLID.md#**I**nterface%20Segregation%20Principle%20(ISP)|Interface%20Segregation%20Principle).
## Using Command Pattern
The solution is to decouple the requester (here, `RemoteControl`) of an action from the request itself. The request is considered as a stand alone object with all the information needed to fulfill it (what device, what action, device location (bathroom, bedroom)). It is passed on to a receiver (here device type like `Light`, `Fan`) which performs the action encapsulated in the request object. This way, the `RemoteControl` does not know what action it is performing, making it more flexible.

So, instead of _only_ considering different devices, we _also_ consider, the different actions one can take. Thus, all actions are implemented from the `Command` interface.
```java
public interface Command {
	public void execute();
}

public class LightOnCommand implements Command {
	Light light;
	
	public LightOnCommand(Light light) {
		this.light = light;
	}	
	
	public void execute {
		light.on();
	}
}

public class NoCommand implements Command {
	public void execute() { }
}

public class RemoteControl {
	Command[] onCommands;
	Command[] offCommands;
	
	public RemoteControl() {
		onCommands = new Command[7];
		offCommands = new Command[7];
		
		// slot is empty
		Command noCommand = new NoCommand();
		for (int i = 0; i < 7; i++) {
			onCommands[i] = noCommand;
			offCommands[i] = noCommand;
		}
	}

	public void setCommand(int slot, Command onCommand, Command offCommand) {
		onCommands[slot] = onCommand;
		offCommands[slot] = offCommand;
	}
	
	// Hardware calls correct function
	public void onButtonWasPushed(int slot) {
		onCommands[slot].execute();
	}
	
	public void offButtonWasPushed(int slot) {
		offCommands[slot].execute();
	}	
}
```

With this, we have decoupled the `RemoteControl` class from the requested action using the `Command` interface. Instead of setting devices we set the actions that will be performed on clicking on/off buttons. Since we use the higher level dependency `Command` in `RemoteControl` , it is unaware of what device and what command is bound to a slot.

Note that, when setting a slot of the remote control, we are ___NOT___ setting the device, but we are setting the on/off commands. This is how we can run this code:
```java
public class RemoteControlTest {
	public static void main(String[] args) {
		RemoteControl remoteControl = new RemoteControl();
		Light livingRoomLight = new Light(“Living Room”);
		Light kitchenLight = new Light(“Kitchen”);
		CeilingFan ceilingFan= new CeilingFan(“Living Room”);
		GarageDoor garageDoor = new GarageDoor(“”);
		Stereo stereo = new Stereo(“Living Room”);

		LightOnCommand livingRoomLightOn =
			new LightOnCommand(livingRoomLight);
		LightOffCommand livingRoomLightOff =
			new LightOffCommand(livingRoomLight);
		
		LightOnCommand kitchenLightOn =
			new LightOnCommand(kitchenLight);
		LightOffCommand kitchenLightOff =
			new LightOffCommand(kitchenLight);
		
		...
		
		remoteControl.setCommand(0, livingRoomLightOn, livingRoomLightOff);
		remoteControl.setCommand(1, kitchenLightOn, kitchenLightOff);
		remoteControl.setCommand(2, ceilingFanOn, ceilingFanOff);
		remoteControl.setCommand(3, stereoOnWithCD, stereoOff);
	
		...
	
		remoteControl.onButtonWasPushed(0);
		remoteControl.offButtonWasPushed(0);
		remoteControl.onButtonWasPushed(1);
	}
}		
```
Recap of workflow: ![Screenshot 2025-12-21 at 2.14.04 PM](Assets/Screenshot%202025-12-21%20at%202.14.04%20PM.png)
## Supporting undo
We extend the `Command` interface functionality to support undo operation. Each subclass of `Command` can then define its own undo operation (ex: Undoing _light on_ results in _light off_).
```java hl:3 hl:17-19
public interface Command {
	public void execute();
	public void undo();
}

public class LightOnCommand implements Command {
	Light light;
	
	public LightOnCommand(Light light) {
		this.light = light;
	}	
	
	public void execute() {
		light.on();
	}
	
	public void undo() {
		light.off();
	}
}
```

To integrate undo operation into `RemoteControl` , we need to maintain last executed command separately.
```java hl:4,11,26,31,34-36
public class RemoteControl {
	Command[] onCommands;
	Command[] offCommands;
	Command undoCommand;
	
	public RemoteControl() {
		onCommands = new Command[7];
		offCommands = new Command[7];
		// slot is empty
		Command noCommand = new NoCommand();
		undoCommand = noCommand;
		for (int i = 0; i < 7; i++) {
			onCommands[i] = noCommand;
			offCommands[i] = noCommand;
		}
	}

	public void setCommand(int slot, Command onCommand, Command offCommand) {
		onCommands[slot] = onCommand;
		offCommands[slot] = offCommand;
	}
	
	// Hardware calls correct function
	public void onButtonWasPushed(int slot) {
		onCommands[slot].execute();
		undoCommand = onCommands[slot];
	}
	
	public void offButtonWasPushed(int slot) {
		offCommands[slot].execute();
		undoCommand = offCommands[slot];
	}
	
	public void undoButtonWasPushed() {
		undoCommand.undo();
	}	
}
```
## Supporting complex operations
To support complex operations like executing multiple `Command` objects, at the click of a button, we can create a subclass of `Command` interface that stores a set of commands to execute as follows:
```java
public class MacroCommand implements Command {
	Command[] commands;
	
	public MacroCommand(Command[] commands) {
		this.commands = commands;
	}	
	
	public void execute() {
		for (int i = 0; i < commands.length; i++) {
			commands[i].execute();
		}
	}
	
	public void undo() {
		for (int i = commands.length - 1; i >= 0; i--) {
			commands[i].undo();
		}
	}
}
```

Then, to set this command to a slot, we must create two instances of `MacroCommand` , one for _on_ button and the other for _off_.
```java hl:16-22
public class RemoteControlTest {
	public static void main(String[] args) {
		RemoteControl remoteControl = new RemoteControl();
		Light light = new Light(“Living Room”);
		TV tv = new TV(“Living Room”);
		Stereo stereo = new Stereo(“Living Room”);
		Hottub hottub = new Hottub();

		LightOnCommand lightOn = new LightOnCommand(light);
		StereoOnCommand stereoOn = new StereoOnCommand(stereo);
		TVOnCommand tvOn = new TVOnCommand(tv);
		HottubOnCommand hottubOn = new HottubOnCommand(hottub);	
			
		...
		
		Command[] partyOn = { lightOn, stereoOn, tvOn, hottubOn};
		Command[] partyOff = { lightOff, stereoOff, tvOff, hottubOff};

		MacroCommand partyOnMacro = new MacroCommand(partyOn);
		MacroCommand partyOffMacro = new MacroCommand(partyOff);
		
		remoteControl.setCommand(0, partyOnMacro, partyOffMacro);
		...
	
		remoteControl.onButtonWasPushed(0);
		remoteControl.offButtonWasPushed(0);
		remoteControl.onButtonWasPushed(1);
	}
}		
```

# UML Diagram
![Screenshot 2025-12-21 at 2.38.18 PM](Assets/Screenshot%202025-12-21%20at%202.38.18%20PM.png)