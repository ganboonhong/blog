---
title: "Understanding Go Interface With Lego"
description: "The purpose of interface is to enable unrelated objects to communicate with each other. In order to communicate, both sides must follow a protocol. Like Lego, in order to put 2 pieces of brick together, you need to make sure they can communicate with each other. The bottom one's studs will fit the one on top's tubes. The designers (programmers) define the interfaces to let two bricks (objects) to communicate."
date: 2022-09-25T14:02:55+08:00
thumbnail: "/blog/images/golang/lego.webp"

tags:
    - golang
    - interface
---

{{< imgborder "images/golang/lego.webp">}}

The purpose of interface is to enable unrelated objects to communicate with each other. In order to communicate, both sides must follow a protocol. Like Lego, in order to put 2 pieces of brick together, you need to make sure they can "communicate" with each other. The bottom one's studs will fit the one on top's tubes. The designers (programmers) define the interfaces to let two bricks (objects) to communicate.

Interface in Go works slightly different from other languages. The way your type implements the interface is implicit. As long as your type implements the same methods that declared in the interface. For example, let's define a `Washer` interface and it has 2 methods: `GetCleaner()` and `GetWaterVolume()`.

```golang
package main

import (
	"fmt"
)

type ClothWasher struct {
	target string
}

type DishWasher struct {
	target string
}

type Washer interface {
	GetCleaner() string
	GetWaterVolume() int
}

func StartWashing(w Washer) string {
	cleaner := w.GetCleaner()
	progress := fmt.Sprintf("start washing with %s", cleaner)

	return progress
}

func StopWashing(w Washer) string {
	vol := w.GetWaterVolume()
	progress := fmt.Sprintf("rinsing with %d liters of water", vol)

	return progress
}

func (d DishWasher) GetCleaner() string {
	return "dishwashing liquid"
}

func (d DishWasher) GetWaterVolume() int {
	return 2
}

func (c ClothWasher) GetCleaner() string {
	return "detergent"
}

func (c ClothWasher) GetWaterVolume() int {
	return 10
}

func main() {
	c := ClothWasher{}
	d := DishWasher{}

	fmt.Println(StartWashing(c)) // start washing with detergent
	fmt.Println(StopWashing(c)) // rinsing with 10 liters of water
	fmt.Println(StartWashing(d)) // start washing with dishwashing liquid
	fmt.Println(StopWashing(d)) // rinsing with 2 liters of water
}
```

Now both `StartWashing` and `StopWashing` accept Washer interface as parameter. Which means as long as a type has both `GetCleaner()` and `GetWater()` methods, this type has already implicitly implemented Washer interface and able to use `StartWashing` and `StopWashing` methods directly.

 

In Go, You may also take the advandage of empty interface in your function paramter. An empty interface as paramter means you can pass in any type of data. But make sure you use it appropriately. For example `fmt` package's `Println` function takes an empty interface as parameter, which means it can print out any data type. By receiving empty interface in incorrect circumstances might also cause unexpected bug.
