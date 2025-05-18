---
title: Software Design - Observer Pattern
date: 2025-05-01 14:00:00 +0530
author: ankitz007
categories: [Software Design Patterns, Behavioral]
tags: [Software Design Patterns]
description: Explore the Observer Pattern, its structure, use cases, and benefits in software design.
---

## Definition

🎥 Imagine you’re a YouTuber (the Subject).  
You post new videos whenever you have something cool to share — vlogs, gameplays, tutorials, etc. 🎮📸📚

Now you’ve got subscribers:

- A student who watches tutorials 📚
- A gamer who loves your let’s plays 🎮
- A chef who just likes your vibe 🍳

These subscribers are the **Observers** 👀.

**Relating to the pattern:**

- You (the YouTuber) → **Subject**
- Subscribers → **Observers**
- Uploading a new video → **State change**
- Notifications going out → **Automatic updates to observers**

**The Observer Pattern** is a behavioral design pattern that defines a one-to-many dependency between objects. When one object (the subject) changes its state, all dependent objects (observers) are automatically notified and updated.

## Structure

![Observer Pattern Structure](https://res.cloudinary.com/ankitz007/image/upload/v1747567591/software-design-patterns/observer_structure_eulx4r.webp){: style="border-radius: 10px; width: 800px;" .center}
_Observer Pattern Structure taken from [Refactoring Guru](https://refactoring.guru/)_

**Main Components**:

- **Publisher** — Maintains a list of subscribers and notifies them of state changes.
- **Subscriber** — Interface for objects that should be notified of changes.
- **Client** — Creates the publisher and subscribers, and establishes the relationship.
- **Notification** — The message or data sent to observers when the subject's state changes.

## Key Characteristics

- **Loose Coupling**  
  The subject and observers only know each other's interfaces, not concrete implementations.  
  Benefit: Enhances modularity and maintainability.

- **Dynamic Relationships**  
  Observers can be added/removed at runtime without affecting the subject.  
  Benefit: Increases flexibility and adaptability.

- **Automatic Updates**  
  Observers get notified whenever the subject's state changes, no manual effort needed.  
  Benefit: Real-time synchronization and minimal risk of outdated data.

- **Broadcast Communication**  
  The subject iterates over all observers and calls a common update method.  
  Benefit: Efficient and consistent notification to all dependents.

## When to Use?

✅ **Multiple components need to stay in sync with shared data**  
Example: Weather station pushing updates to apps, displays, and websites.

✅ **Real-time event-driven updates are required**  
Example: Stock trading platforms pushing live updates.

✅ **Publish/subscribe communication**  
Example: Chat server broadcasting messages to all group members.

✅ **Separation of data logic and UI**  
Example: To-do list apps updating UI when tasks are modified.

## When NOT to Use?

❌ **Observer relationships are too complex**  
Hard to debug when observers depend on multiple subjects.

❌ **Extremely frequent updates where performance is critical**  
May lead to performance bottlenecks ("update storm").

❌ **Updates are rare and real-time isn't needed**  
Polling might be simpler and more efficient.

❌ **Observers can trigger updates back to subjects**  
Risks infinite loops due to circular dependencies.

❌ **Tight synchronization or specific ordering needed**  
Observer pattern doesn't guarantee notification order or precise timing.

## Code Example

```python
# Subject
class Subject:
    def __init__(self):
        self._observers = []
    def attach(self, observer):
        self._observers.append(observer)
    def notify(self, message):
        for observer in self._observers:
            observer.update(message)

# Observer
class Observer:
    def update(self, message):
        pass  # To be implemented by concrete observers

# Concrete Observers
class EmailObserver(Observer):
    def update(self, message):
        print(f"📧 Email received: {message}")
class SMSObserver(Observer):
    def update(self, message):
        print(f"📱 SMS received: {message}")

# Usage
if __name__ == "__main__":
    subject = Subject()
    email_observer = EmailObserver()
    sms_observer = SMSObserver()
    subject.attach(email_observer)
    subject.attach(sms_observer)
    # State change triggers notification
    subject.notify("New Promotion: 50% OFF Sale!")
```

## Real World Examples

- **Social Media Feeds** (Twitter/X, Facebook, Instagram) 📱  
  - Subject: User's profile/account
  - Observers: Followers' feeds
  - Flow: New posts automatically update followers' feeds.

- **Weather Monitoring Systems** 🌧❄  
  - Subject: Weather station
  - Observers: Apps, websites, agricultural systems
  - Flow: Updates pushed to all subscribed platforms.

- **Event Management Systems** 🎟  
  - Subject: Event status (e.g., sold-out, new tickets)
  - Observers: Ticket vendors, sponsors, social media
  - Flow: Changes notify all related parties.

- **Online Banking Systems** 💰  
  - Subject: Bank account (balance changes)
  - Observers: Mobile app, SMS service, email alerts
  - Flow: Notifications sent when balance changes.

- **Health Monitoring Systems** 🏥  
  - Subject: Health monitor tracking vital signs
  - Observers: Doctor's dashboard, patient app
  - Flow: Critical changes alert emergency systems.

- **IoT Sensor Networks** 🌐  
  - Subject: SensorHub aggregating sensor data
  - Observers: Dashboards, alert systems
  - Flow: Changes immediately reflected across systems.
