#system-design #lld #design-patterns #java 
# Definition
The Observer Pattern defines a one-to-many dependency between objects such that when the state of an object changes, all of its dependents are notified and updated automatically.
# Need for Observer Pattern
It is used when we some functionality in our application needs a publisher-subscriber model:
	_Subscribers_ subscribe to whatever _publisher_ is publishing.
	Every time _publisher_ makes an update, _subscribers_ are notified of the update.
	_Subscribers_ can unsubscribe anytime and stop receiving updates. They can also resubscribe anytime to receive updates again.

Consider that you want to create a weather application that gets data such as _temperature, pressure_ and _humidity_ from a weather station. 
The application has 3 displays, _current conditions_, _weather stats_ and _forecast_, which must be updated whenever weather measurements taken by the weather station changes.(`measurementsChanged()`)
The system must be extensible so that we can add/remove displays with ease.
![[Pasted image 20250930151657.png]]
# Observer Pattern
>[!note]
>Publishers are called **subjects** and subscribers are called **observers** in this pattern.

Following the pub-sub model, it stands to reason that the **observers** in this case would be our displays: `CurrentConditionsDisplay`, `StatisticsDisplay` and `ForecastDisplay` which need to updated whenever there is some change in measurements, and the **subject** is the weather station `WeatherData` which will be responsible for notifying the **observers** that there has been a change in the measurements.
## Subject Interface
Assuming that the weather station `WeatherData` keeps measurements for a particular locality only, what if in the future, the application needs to support weather data from multiple locations. We would need to gather data from multiple sources, with each source being a **subject**.

Keeping this in mind, we need to create a `Subject` interface which defines some common functionality between concrete subjects such as the ability to add observers, remove observers and notify observers when there is an update.
This ensures we can easily extend our application to support new subjects., when needed.

```java
public interface Subject {
	public void registerObserver(Observer o);
	public void removeObserver(Observer o);
	public void notifyObservers();
}
```

The concrete subject `WeatherData` implements this interface to provide observers with updates.

```java hl:11-27
public class WeatherData implements Subject {
	private List<Observer> observers;
	private float temperature; 
	private float humidity; 
	private float pressure;
	
	public WeatherData() {
		observers = new ArrayList<>();
	}
	
	public void registerObserver(Observer o) { 
		observers.add(o);
	}

	public void removeObserver(Observer o) { 
		int i = observers.indexOf(o);  
		if (i >= 0) {
			observers.remove(i); 
		}
	}
	
	public void notifyObservers() {  
		for (int i = 0; i < observers.size(); i++) {
			Observer observer = (Observer)observers.get(i);
			observer.update(this);  
		}  
	}
	
	public void measurementsChanged() {  
		notifyObservers();
	}
	//other methods
}
```
## Observer Interface
Similarly, if we want to add a new kind of display which will need weather data, it is easier to do we create an `Observer` interface with some common functionality that the concrete classes `CurrentConditionsDisplay`, `StatisticsDisplay` and `ForecastDisplay` can implement.

```java
public interface Observer {
	public void update(Subject weatherData);
}
```

and a concrete class would implement it as follows:
```java
public class CurrentConditionsDisplay implements Observer {
	private float temperature;
	private float humidity;
	private Subject weatherData; // can be used to unsubscribe too
	
	//[[Core Principles#**Constructor injection**|Constructor injection]]
	public CurrentConditionsDisplay(Subject weatherData) {
		this.weatherData = weatherData;
		weatherData.registerObserver(this);
	}
	
	public void update(Subject weatherData) {
		this.temperature = weatherData.getTemperature();
		this.humidity = weatherData.getHumidity();
		display();
	}
	
	public void display() {
		System.out.println(“Current conditions: “ + temperature
+ “F degrees and “ + humidity + “% humidity”);
	}
}
```
# Java's Inbuilt Observer Pattern
+ Can be implemented using the `java.util.Observer` interface (replacing `Observer`) and `java.util.Observable` **class** (replacing `Subject` ).
+ For `Observable` to send notifications, its `setChanged()` method must first be set to true. If `notifyObservers() / notifyObservers(Object arg)` is called without setting `setChanged` to true, no notifications are sent to observers.
+ Since `Observable` is a class, it must be subclassed to create a concrete subject.
	+ Concrete Subject cannot extend any other class, which limits flexibility.
	+ Since a `Observable` interface does not exist, we cannot create custom implementation which can be used with `java.util.Observer` interface.
	+ Since `setChanged()` method is _protected_, we cannot [[Composition|compose]] an instance of `Observable` with our own class to create a custom implementation, since _protected_ methods can only be accessed by the class itself and its subclasses.
+ 
# UML Class Diagram
![[Screenshot 2025-02-20 at 5.23.50 PM.png]]
# Advantages
+ `Subject` and `Observer` are both loosely coupled. `Subject` is unaware of what the concrete Observer does, or wants.
+ We can add new observers at any time, no need to modify `Subject` .
+ We can reuse subjects or observers independently of each other.
+ Changes to either the subject or an observer will not affect the other.
# Disadvantages
+ Subscribers are notified in a random order.