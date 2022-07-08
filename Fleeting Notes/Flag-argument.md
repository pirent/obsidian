# Flag argument
Created: 2022-07-02 16:56

## Definition
A flag argument tells the function to carry out a different operation depending on its value.
```java
public Booking book (Customer aCustomer, boolean isPremium) {..**.**}
```

❌ Avoid them
✔ Define separate methods
```java
public Booking regularBook(Customer aCustomer) {..**.**}

public Booking premiumBook(Customer aCustomer) {..**.**}
```

## Reasons
It makes the implementation become tangled.
```java
public Booking book (Customer aCustomer, boolean isPremium) {
	lorem().ipsum();
	dolor();
	**if(isPremium)**
		sitAmet();
	consectetur();
	**if(isPremium)**
		adipiscing().elit();
	**else {**
		aenean();
		vitaeTortor().mauris();
	**}**
	eu.adipiscing();
```
It can be messy when trying extracting the regular and premium book methods into separate methods without significant duplication between the two.

## Resolution
🌟 If the decision on whether to use a premium booking process depends upon **the status of the customer,** use the customer object as the flag.
- the caller doesn't need to care about the difference between premium and regular booking
- the booking routine can derive it's true method based on the customer status

## References
1.

---
tags: #design-pattern #anti-design-pattern