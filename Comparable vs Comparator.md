| Features         | Comparable                                    | Comparator                              |
| ---------------- | --------------------------------------------- | --------------------------------------- |
| Definition       | It defines natural ordering within the class. | It defines external sorting logic.      |
| Method           | compareTo()                                   | compare()                               |
| Implementation   | It is implemented in the class.               | It is implemented in a separate class.  |
| Sorting Criteria | Natural order sorting                         | Custom sorting                          |
| Usage            | It is used for single sorting order.          | It is used for multiple sorting orders. |
# Comparable
```java
class Movie implements Comparable<Movie> {
	 public int compareTo(Movie m) {
      
        // Sort movies in ascending 
        // order of year
        return this.y - m.y; 
    }
	public static void main(String args[]){
		Collections.sort(l); // sort() internally calls compareTo method
	}
}
```

# Comparator
```java
// Comparator to sort movies by rating
class Rating implements Comparator<Movie> {
    public int compare(Movie m1, Movie m2) {
      
        // Sort by rating in descending order
        return Double.compare(m2.getR(), m1.getR());
    }
}

// Comparator to sort movies by name
class NameCompare implements Comparator<Movie> {
    public int compare(Movie m1, Movie m2) {
      
        // Sort by name in alphabetical order
        return m1.getN().compareTo(m2.getN()); 
    }
}

public static void main(String args[]){
		// Sort movies by rating 
        Collections.sort(m, new Rating());
        // Sort movies by name and display all
        Collections.sort(m, new NameCompare());
	}
```