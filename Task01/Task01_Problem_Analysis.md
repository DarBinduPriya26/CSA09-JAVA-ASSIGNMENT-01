# Task 01 – Understand the Problem and Identify the Objects

**Assignment:** Campus Smart-Parking System  
**Course Outcome:** CO1 – Java Fundamentals and Object-Oriented Programming  
**Student Register Number:** 192425162

## 1. Problem Analysis

The campus smart-parking system manages a multi-floor parking facility used by students, staff and visitors. It supports two-wheelers, cars and electric vehicles. Each floor contains a fixed number of parking slots. When a vehicle enters, the system must find a suitable free slot, issue a unique parking ticket and record the entry time. When the vehicle leaves, the system must release the slot and calculate the parking fee according to the vehicle type and parking duration. Valid monthly-pass holders who are students or staff park free of charge, while visitors pay the applicable fee. Electric vehicles may use charging slots, for which a separate charging fee is calculated.

The design should support changes such as additional vehicle categories and new pricing rules without requiring unnecessary changes to existing classes.

## 2. Inputs and Outputs

### Inputs
- Vehicle registration number.
- Vehicle category: two-wheeler, car or electric vehicle.
- User category: student, staff or visitor.
- Monthly-pass validity details, where applicable.
- Entry time and exit time.
- Floor and slot details when a slot is released or checked.
- Charging energy consumed in kWh for an electric vehicle, where applicable.

### Outputs
- Allocated floor number and slot number.
- Unique parking ticket number.
- Recorded entry and exit times.
- Parking duration.
- Parking fee and any separate charging fee.
- Updated slot availability and facility occupancy.
- A message when no suitable slot is available or an invalid operation is attempted.

## 3. Candidate Classes and Their Responsibilities

| Candidate class | Responsibility | Why it is needed |
|---|---|---|
| `ParkingFacility` | Stores floors and slots; allocates and releases slots; tracks occupancy | Keeps parking-space management in one place |
| `ParkingFloor` | Represents one floor and its slots | Allows floors to have different slot counts |
| `ParkingSlot` | Stores slot number, slot type and occupancy status | Prevents a slot from being assigned to more than one vehicle |
| `Vehicle` | Stores common vehicle information such as registration number | Provides a common model for all vehicle categories |
| `TwoWheeler` | Represents a two-wheeler and its applicable rate | Allows category-specific pricing |
| `Car` | Represents a car and its applicable rate | Allows category-specific pricing |
| `ElectricVehicle` | Represents an EV and charging capability | Supports charging-slot allocation and separate charging fees |
| `ParkingTicket` | Stores ticket number, vehicle, slot, entry time and exit details | Records one parking visit and supports fee calculation |
| `ParkingUser` | Stores user category and pass information | Separates student/staff/visitor eligibility from vehicle details |
| `MonthlyPass` | Stores pass validity and holder information | Checks whether a student or staff member qualifies for free parking |
| `PricingPolicy` | Calculates parking charges according to rules | Keeps pricing rules separate and easier to extend |

These are candidate classes for the design. A small first implementation may combine `ParkingFloor` and `ParkingSlot` into the `ParkingFacility` representation, but separate classes are easier to maintain as the system grows.

## 4. Candidate Methods

| Method | Class | Purpose |
|---|---|---|
| `allocateSlot(vehicle)` | `ParkingFacility` | Finds and reserves a suitable free slot |
| `releaseSlot(floor, slot)` | `ParkingFacility` | Frees an occupied slot after validating its position |
| `isAvailable()` | `ParkingSlot` | Checks whether the slot is free |
| `park(vehicle, slot)` | `ParkingSlot` | Assigns a vehicle to a free slot |
| `removeVehicle()` | `ParkingSlot` | Clears the vehicle from the slot |
| `generateTicket()` | `ParkingTicket` | Creates a ticket with a unique number |
| `calculateDuration()` | `ParkingTicket` | Calculates the parking duration |
| `calculateFee(duration)` | `PricingPolicy` or `Vehicle` | Calculates the applicable parking fee |
| `isValid()` | `MonthlyPass` | Checks pass validity |
| `chargingFee(kWh)` | EV charging capability | Calculates the separate charging fee |

## 5. Business Rules

1. A parking slot can contain at most one vehicle at a time.
2. A vehicle cannot be allocated to an occupied slot.
3. A ticket number must be unique and must never be reused during the system's lifetime.
4. A vehicle registration number must not have more than one active parking session.
5. A vehicle must be assigned only to a slot suitable for its category.
6. A slot can be released only when the floor and slot indexes are valid and the slot is occupied.
7. Parking duration cannot be negative.
8. Fee calculation must use the correct vehicle rate and the defined billing rule.
9. A valid monthly pass gives free parking only to eligible students and staff, according to the assignment scenario.
10. Visitors pay the applicable parking fee.
11. EV charging fees are calculated separately from the parking fee.
12. When no suitable slot is available, the system must report that allocation failed and must not issue an active parking ticket.

## 6. Assumptions

- Floor and slot indexes inside the program start at zero; displayed floor and slot numbers may start at one.
- Each floor has a fixed number of slots during one program run, but different floors may have different slot counts.
- Registration numbers are treated as unique identifiers and are stored in a consistent format.
- Entry and exit times are recorded using Java date/time classes.
- A started-hour billing rule will be used only where the selected pricing policy explicitly defines it; the exact rates and slabs must be configured separately.
- A monthly pass is free only when it is valid at the time of parking and belongs to a student or staff member.
- The assignment does not specify exact vehicle rates, charging rates, grace periods or daily maximum values. These values must therefore be stated as configurable assumptions in the implementation rather than treated as facts from the question.
- The first available suitable slot is allocated unless a later requirement defines a different allocation strategy.

## 7. Alternative Concept: Plain Strings in Parallel Arrays

Instead of creating a `Vehicle` object for each vehicle, the system could store vehicle details in parallel arrays:

```java
String[] regNo = new String[capacity];
String[] type = new String[capacity];
String[] entryTime = new String[capacity];
```

The information for one vehicle is stored at the same array index in all three arrays. For example, `regNo[2]`, `type[2]` and `entryTime[2]` describe the same vehicle.

### Finding a vehicle by registration number

The program would scan the `regNo` array from index zero to the end, comparing each value with the requested registration number. If the vehicle is found, the same index is used to access its type and entry time.

### Comparison

| Factor | Vehicle objects | Parallel arrays |
|---|---|---|
| Search by registration number | Linear scan: O(n) without an index | Linear scan: O(n) |
| Access related details | Read fields/methods from one object | Must use the matching index in every array |
| Adding a new attribute | Add a field to `Vehicle` and update constructors/accessors | Create another array and keep every index aligned |
| Risk of inconsistent data | Lower when fields are encapsulated | Higher if array positions become misaligned |
| Space per vehicle | O(1) fields per object; total O(n) | O(1) entries per array; total O(n) |
| Extensibility | Supports inheritance and polymorphism | Requires extra arrays and type-specific conditions |

### Complexity Analysis

Let `n` be the number of vehicles stored.

- Searching by registration number in either design takes **O(n)** time in the worst case because every registration may need to be checked.
- Adding one vehicle to an available array position takes **O(1)** time when a free index is already known.
- Both designs use **O(n)** total storage for `n` vehicles.
- Adding a new attribute to the object design requires changing the vehicle model. In the parallel-array design, it requires another array and updates to every operation that inserts, removes or reads vehicle data.

### Conclusion

The object-oriented design is preferred because vehicle information and behaviour are grouped together. It supports encapsulation, inheritance and polymorphism, and it is easier to extend when new vehicle categories or attributes are introduced. Parallel arrays can represent the same basic data, but they make it easier for related values to become misaligned and require more coordinated changes when the model grows.

## 8. Result

The campus smart-parking problem has been analysed. The main inputs, outputs, candidate classes, methods, business rules and assumptions have been identified. The object-oriented approach is selected because it provides a clearer and more extensible model than parallel arrays.
