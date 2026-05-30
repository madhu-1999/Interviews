#lld #design-patterns #system-design #java 
# Definition
The Facade Pattern provides a unified interface to a set of interfaces in a subsytem. Facade defines a higher-level interface that makes the subsystem easier to use.
# Facade Pattern
Suppose you have a home theater consisting of multiple components (Amplifier, Tuner, Projecter, Screen etc...).
To watch a movie, we would have to perform multiple steps like so:
```java
// Get the popcorn ready
popper.on();
popper.pop();
//Dim the lights to 10%
lights.dim(10);
//Set up the projector
projector.on();
projector.setInput(dvd);
projector.wideScreenMode();
//Adjust the volume
amp.on();
amp.setDvd(dvd);
amp.setSurroundSound();
amp.setVolume(5);
//Play the movie
dvd.on();
dvd.play(movie);
```

If in the future, we upgrade some of the components, it is possible that we have to perform the steps slightly differently. This means, we would have to modify the code, violating [[SOLID#**O**pen for extension, closed for modification|Open/closed principle]].

To avoid this, a simple solution would be to encapsulate these steps in a function, and use it whenever, we want to watch a movie. This way, any changes after upgrade, are limited to the function.
But, other functionality like turning off the home theater system would be equally complex involving multiple steps, which could again be simplified by wrapping it in a function.

Extending this idea, we can create a interface/class for the home theater system which handles these complex operations and decouples all the home theater operations from the rest of the code. This interface is the Facade pattern.
```java
public class HomeTheaterFacade {
	Amplifier amp;
	Tuner tuner;
	DvdPlayer dvd;
	CdPlayer cd;
	Projector projector;
	TheaterLights lights;
	Screen screen;
	PopcornPopper popper;
	
	public HomeTheaterFacade(Amplifier amp,
		Tuner tuner,
		DvdPlayer dvd,
		CdPlayer cd,
		Projector projector,
		Screen screen,
		TheaterLights lights,
		PopcornPopper popper) {
			
			this.amp = amp;
			this.tuner = tuner;
			this.dvd = dvd;
			this.cd = cd;
			this.projector = projector;
			this.screen = screen;
			this.lights = lights;
			this.popper = popper;
	}

	public void watchMovie(String movie) {
		System.out.println(“Get ready to watch a movie...”);
		popper.on();
		popper.pop();
		
		lights.dim(10);
		
		screen.down();
		projector.on();
		projector.wideScreenMode();
		
		amp.on();
		amp.setDvd(dvd);
		amp.setSurroundSound();
		amp.setVolume(5);
		
		dvd.on();
		dvd.play(movie);
	}
	
	public void endMovie() {
		System.out.println(“Shutting movie theater down...”);
		popper.off();
		lights.on();
		screen.up();
		projector.off();
		amp.off();
		dvd.stop();
		dvd.eject();
		dvd.off();
	}
}
```
If required, we can still access any of the subsystems (`Amplifier`, `Screen` etc..) individually. Facade pattern is just simplifying access to the combined system.

# UML Diagram
![[Screenshot 2025-12-25 at 1.36.34 AM.png]]