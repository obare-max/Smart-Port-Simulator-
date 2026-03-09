# smart_port.py
# -----------------------------------------
# Smart Port Logistics Simulator
# A Python-based simulation tool for managing cargo flow in ports.
# Models ships, cargo manifests, warehouses, and unloading operations.
# Generates efficiency reports and compliance checks.
# -----------------------------------------

from datetime import datetime

# -------------------------------
# Ship class: Represents a vessel arriving at the port
# -------------------------------
class Ship:
    def __init__(self, name, arrival, cargo_manifest):
        # Ship name
        self.name = name
        # Arrival date (converted to datetime object)
        self.arrival = datetime.strptime(arrival, "%Y-%m-%d")
        # List of cargo items carried by the ship
        self.cargo_manifest = cargo_manifest
        # Status flag to track if cargo has been unloaded
        self.unloaded = False

    def __repr__(self):
        return f"Ship({self.name}, Arrival: {self.arrival.date()}, Cargo: {len(self.cargo_manifest)} items)"


# -------------------------------
# Warehouse class: Stores cargo unloaded from ships
# -------------------------------
class Warehouse:
    def __init__(self, capacity):
        # Maximum storage capacity
        self.capacity = capacity
        # Current stored cargo
        self.storage = []

    def store(self, cargo):
        # Check if there is enough space for new cargo
        if len(self.storage) + len(cargo) <= self.capacity:
            self.storage.extend(cargo)
            return True
        else:
            print("⚠️ Warehouse full! Cannot store cargo.")
            return False

    def __repr__(self):
        return f"Warehouse(Storage: {len(self.storage)}/{self.capacity})"


# -------------------------------
# Port class: Manages ships and warehouse operations
# -------------------------------
class Port:
    def __init__(self, name):
        # Port name
        self.name = name
        # List of ships scheduled/arrived
        self.ships = []
        # Single warehouse instance for cargo storage
        self.warehouse = Warehouse(capacity=100)

    def add_ship(self, ship):
        # Add a ship to the port schedule
        self.ships.append(ship)
        print(f"🚢 Ship {ship.name} scheduled for arrival on {ship.arrival.date()}.")

    def unload(self, ship):
        # Unload cargo from a ship into the warehouse
        if not ship.unloaded:
            success = self.warehouse.store(ship.cargo_manifest)
            if success:
                ship.unloaded = True
                print(f"✅ Ship {ship.name} unloaded successfully.")
        else:
            print(f"ℹ️ Ship {ship.name} already unloaded.")

    def generate_report(self):
        # Generate a summary report of port operations
        print("\n📊 Port Report")
        print(f"Port: {self.name}")
        print(f"Total Ships: {len(self.ships)}")
        for ship in self.ships:
            status = "Unloaded" if ship.unloaded else "Pending"
            print(f"- {ship.name}: {status}")
        print(self.warehouse)


# -------------------------------
# Example usage (simulation run)
# -------------------------------
if __name__ == "__main__":
    # Create a port instance
    port = Port("Mombasa International Port")

    # Define ships with cargo manifests
    ship1 = Ship("MV Horizon", "2026-03-10", ["containers", "vehicles", "machinery"])
    ship2 = Ship("SS Neptune", "2026-03-12", ["grain", "oil drums"])

    # Add ships to port schedule
    port.add_ship(ship1)
    port.add_ship(ship2)

    # Unload cargo from one ship
    port.unload(ship1)

    # Generate operational report
    port.generate_report()
