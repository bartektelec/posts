---
title: "TypeScript Builder Pattern"
excerpt: "Complex class entities may get difficult to produce using only constructor arguments, providing more options for customization makes the constructor cluttered and difficult to read..."
coverImage: "/assets/builder.jpg"
date: "Tue 20 Oct, 2020"
tags: "typescript, design patterns, creational"

ogImage:
  url: "/assets/builder.jpg"
---

## Intro

Builder is a creational pattern, that helps us avoid creating complex class constructors, using easy to read setter methods instead. It allows us to produce different types of objects using the same constructor code.

## Problem

![Confused builder](https://media.giphy.com/media/VIJxPwIvOsmL4XTikd/giphy-downsized.gif)

Complex class entities may get difficult to produce using only constructor arguments, providing more options for customization makes the constructor cluttered and difficult to read.

```ts
// Creating a red Enzo Ferrari with v12 6L engine, 2 doors, 1480kg weight and 651hp
const sportsCar = new Car("Ferrari", "Enzo", "red", "v12", 6000, 651, 2, 1480);
// or
const sportsCar = new Car({
  brand: "Ferrari",
  model: "Enzo",
  engine: { type: "v12", volume: 6000, hp: 651 },
  doors: 2,
  weight: 1480,
});
// results in error: color was not defined
```

## Solution

#### Fluent

![man rolling a long list](https://media.giphy.com/media/F0QWePzwQRewM/giphy.gif)

Using static class methods makes it easier to understand what we're building, by doing it step-by-step. It is like reading a blueprint.

```ts
// Using Fluent Builder pattern
// Great if the implementation will not be repeated many times
const sportsCar = new CarBuilder()
  .setBrand("Ferrari")
  .setModel("Enzo")
  .setColor("red")
  .setEngine({ type: "v12", volume: 6000, hp: 651 })
  .setDoors(2)
  .setWeight(1480)
  .getCar();
```

#### Concrete builders and directors

![Im doing my part animation](https://media.giphy.com/media/7T2R1eAIKnEJnAf5U8/giphy.gif)

Builder can be served as a complete blueprint aka. `Concrete Builder`, these classes are supposed to provide their own methods for creating parts of the instance and can be based on a Fluent Builder Pattern.

These `Concrete Builders` when passed to a `Director class` will be read and built in the same order every time no matter what blueprint we pass to the `Director`, however `Concrete Builder` methods will decide how the final instance will look like.

```ts
// Using Classic Builder pattern with Director
// Creating blueprints for our builds and passing it to the director
const carBuilder = new FerrariBuilder();
const carDirector = new Director(carBuilder);

carDirector.buildCabriolet();
carBuilder.getProduct(); // Returns a Ferrari object with no roof

carDirector.buildFullCar();
carBuilder.getProduct(); // Returns a Ferrari object with roof
```

## Code examples

### Fluent builder

```ts
class CarBuilder {
  private brand: string;
  private model: string;
  private engine: Engine;
  private color: Color;

  constructor() {
    this.element = new Car();
  }

  public setBrand(brand: string): Car {
    this.brand = brand;
    return this;
  }

  public setEngine(engine: Engine): Car {
    this.engine = engine;
    return this;
  }

  public setColor(color: Color): Car {
    this.color = color;
    return this;
  }

  public getProduct(): Car {
    return new Car(this);
  }
}
```

### Concrete builder + Director

#### Concrete builder

```ts
class FerrariBuilder implements Builder {
    private product: Car;

    constructor() {
        this.reset();
    }

    public reset():void {
        this.product = new Car();
    }

    public addDoors():void {
        this.product.setDoors(2);
    }

    public addChassis():void {
        this.product.setColor('red');
    }

    public addEngine():void {
        this.product.setEngine({
            type: 'v12',
            volume: 6000,,
            hp: 651
        })
    }

    public addRoof():void {
        this.product.setRoof(true)
    }

    public getProduct():Car {
        const result = this.product;
        this.reset();
        return result;
    }
}
```

#### Director class

```ts
class CarDirector {
  private builder: Builder;

  constructor(builder: Builder) {
    this.builder = builder;
  }

  public buildCabriolet(): Car {
    this.builder.addChassis();
    this.builder.addDoors();
    this.builder.addEngine();
  }

  public buildFullCar(): Car {
    this.builder.addChassis();
    this.builder.addDoors();
    this.builder.addEngine();
    this.builder.addRoof();
  }
}
```

## Relations with other patterns

To me this pattern reminds me of strategy, it is based on _blueprints_ just as strategy is based on, well _strategies_.
It provides us with an interface that uses similar methods but produces a different result based on what _blueprint_ is given.

### Sources

- https://refactoring.guru/design-patterns/builder
