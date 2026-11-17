# smart_parking_system

from flask import Flask, render_template_string

app = Flask(__name__)

uml_code = """
classDiagram
direction LR

class Address {
    +street_address: string
    +city: string
    +state: string
    +country: string
    +getFullAddress() string
}

class Person {
    +name: string
    +address: Address
    +email: string
    +phone: string
    +updateDetails() bool
    +getContactInfo() string
}

class AccountStatus {
    <<enumeration>>
    ACTIVE
    BLOCKED
    BANNED
    ARCHIVED
}

class Account {
    +user_name: string
    -password: string
    +person: Person
    +status: AccountStatus
    +login() bool
    +logout() void
    +resetPassword() bool
    +updateAccount() bool
}

class Admin {
    +admin_id: string
    +addParkingFloor() bool
    +removeParkingFloor() bool
    +addParkingLot() bool
    +manageAccounts() void
    +generateReport() void
}

class ParkingAttendant {
    +attendant_id: string
    +processTicket() bool
    +verifyPayment() bool
    +assistCustomer() void
}

class ParkingRate {
    <<enumeration>>
    FIRST_HOUR = 4.00
    SECOND_AND_THIRD_HOUR = 3.50
    REMAINING_HOURS = 2.50
    +calculate(hours: double) double
}

class ParkingLot {
    +name: string
    +address: Address
    +floors: List
    +active_tickets: Map
    +getInstance() ParkingLot
    +getNewParkingTicket() ParkingTicket
    +isVehicleInside() bool
    +exitVehicle() bool
    +getAvailableSpots() int
}

class EntrancePanel {
    +panel_id: string
    +parking_lot: ParkingLot
    +printTicket() ParkingTicket
    +scanVehicle() Vehicle
}

class ExitPanel {
    +panel_id: string
    +parking_lot: ParkingLot
    +scanTicket() ParkingTicket
    +processPayment() bool
    +openGate() void
}

class ParkingFloor {
    +floor_id: string
    +name: string
    +floor_number: int
    +spots: List
    +display_board: ParkingDisplayBoard
    +isFull() bool
    +getAvailableSpots() int
    +assignSpot() ParkingSlot
}

class ParkingSlot {
    +slot_id: string
    +is_occupied: bool
    +spot_type: string
    +vehicle: Vehicle
    +parkVehicle() bool
    +removeVehicle() bool
    +isAvailable() bool
}

class HandicappedSpot {
    +accessibility_feature: string
    +reserved: bool
    +checkEligibility() bool
}

class CompactSpot {
    +maximum_vehicle_size: string
    +checkVehicleSize() bool
}

class ParkingDisplayBoard {
    +board_id: string
    +free_counts: Map
    +update() void
    +showFreeSpots() void
    +displayMessage() void
}

class ParkingTicketStatus {
    <<enumeration>>
    ACTIVE
    PAID
    LOST
}

class ParkingTicket {
    +ticket_number: string
    +vehicle: Vehicle
    +issued_at: DateTime
    +paid_at: DateTime
    +status: ParkingTicketStatus
    +calculateDuration() double
    +calculateAmount() double
    +markPaid() void
    +markLost() void
}

class CustomerInfoPortal {
    +portal_id: string
    +parking_lot: ParkingLot
    +displayAvailability() void
    +payTicket() bool
    +viewParkingRates() void
}

class Payment {
    <<abstract>>
    +payment_id: string
    +amount: double
    +payment_time: DateTime
    +processPayment() bool
    +refundPayment() bool
}

class CashTransaction {
    +cash_received: double
    +change_returned: double
    +processPayment() bool
    +calculateChange() double
}

class Vehicle {
    +license_number: string
    +vehicle_type: VehicleType
    +color: string
    +owner: Person
    +getVehicleDetails() string
}

class VehicleType {
    <<enumeration>>
    MOTORCYCLE
    CAR
    TRUCK
    ELECTRIC
}

class ParkingSpotType {
    <<enumeration>>
    COMPACT
    HANDICAPPED
    LARGE
    ELECTRIC
}

Person "1" --> "1" Address
Account --> Person
Account --> AccountStatus
Admin --|> Account
ParkingAttendant --|> Account
ParkingLot --> Address
ParkingLot "1" *-- "*" ParkingFloor
ParkingFloor "1" *-- "*" ParkingSlot
ParkingFloor *-- ParkingDisplayBoard
HandicappedSpot --|> ParkingSlot
CompactSpot --|> ParkingSlot
ParkingLot --> ParkingRate
ParkingLot --> ParkingTicket
ParkingTicket --> Vehicle
ParkingTicket --> ParkingTicketStatus
ParkingLot --> EntrancePanel
ParkingLot --> ExitPanel
CustomerInfoPortal --> ParkingLot
CustomerInfoPortal --> ParkingTicket
ExitPanel --> Payment
CashTransaction --|> Payment
ParkingSlot --> Vehicle
ParkingSlot --> ParkingSpotType
Vehicle --> VehicleType
ParkingAttendant --> ParkingTicket
Admin --> ParkingLot
"""

HTML = """
<!DOCTYPE html>
<html>
<head>
<title>Parking Lot UML Diagram</title>
<script type="module">
import mermaid from
'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';

mermaid.initialize({
    startOnLoad: true,
    theme: 'base',
    themeVariables: {
        primaryColor: '#fff5bd',
        primaryBorderColor: '#8c642d',
        lineColor: '#88452f',
        fontFamily: 'Arial'
    }
});
</script>

<style>
body {
    margin: 0;
    font-family: Arial;
    background: #f2f2f2;
}

header {
    background: #25364a;
    color: white;
    padding: 20px;
    text-align: center;
}

.container {
    margin: 20px;
    padding: 25px;
    background: white;
    overflow: auto;
    border-radius: 8px;
    min-height: 600px;
}

.mermaid {
    min-width: max-content;
    display: flex;
    justify-content: center;
}

button {
    background: #315b80;
    color: white;
    padding: 10px 20px;
    border: none;
    cursor: pointer;
    margin: 10px;
    border-radius: 5px;
}
</style>
</head>

<body>

<header>
<h1>Parking Lot Management System</h1>
<p>Complete UML Class Diagram</p>
</header>

<center>
<button onclick="location.reload()">Refresh Diagram</button>
<button onclick="window.print()">Print Diagram</button>
</center>

<div class="container">
<pre class="mermaid">
{{ uml_code }}
</pre>
</div>

</body>
</html>
"""

@app.route("/")
def home():
    return render_template_string(HTML, uml_code=uml_code)

if __name__ == "__main__":
    app.run(debug=True)
