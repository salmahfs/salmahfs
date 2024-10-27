using System;
using System.Collections.Generic;
using System.Linq;

public class Table
{
    public int TableNumber { get; set; }
    public int Capacity { get; set; }
    public bool IsOccupied { get; set; }
    public string Status { get; set; } // "Available", "Occupied", etc.

    public Table(int tableNumber, int capacity)
    {
        TableNumber = tableNumber;
        Capacity = capacity;
        IsOccupied = false;
        Status = "Available";
    }

    public void UpdateStatus(string status)
    {
        Status = status;
        IsOccupied = status == "Occupied";
    }
}

public class Reservation
{
    public int ReservationId { get; set; }
    public DateTime ReservationTime { get; set; }
    public int NumberOfGuests { get; set; }
    public string CustomerName { get; set; }

    public Reservation(int id, DateTime time, int guests, string name)
    {
        ReservationId = id;
        ReservationTime = time;
        NumberOfGuests = guests;
        CustomerName = name;
    }
}

public class Waitstaff
{
    private List<Table> tables;
    private List<Reservation> reservations;

    public Waitstaff(List<Table> tables, List<Reservation> reservations)
    {
        this.tables = tables;
        this.reservations = reservations;
    }

    public void VerifyReservation(int reservationId)
    {
        var reservation = reservations.FirstOrDefault(r => r.ReservationId == reservationId);
        if (reservation != null)
        {
            Console.WriteLine($"Reservation verified for {reservation.CustomerName} at {reservation.ReservationTime}");
        }
        else
        {
            Console.WriteLine("Reservation not found.");
        }
    }

    public Table CheckTableAvailability(int requiredCapacity)
    {
        var availableTable = tables.FirstOrDefault(t => t.Capacity >= requiredCapacity && t.Status == "Available");
        return availableTable;
    }

    public void AssignTable(Table table)
    {
        if (table != null)
        {
            table.UpdateStatus("Occupied");
            Console.WriteLine($"Table {table.TableNumber} assigned.");
        }
        else
        {
            Console.WriteLine("No table available for the required capacity.");
        }
    }

    public void SeatCustomer(Table table)
    {
        if (table != null)
        {
            table.UpdateStatus("Occupied");
            Console.WriteLine($"Customer seated at table {table.TableNumber}");
        }
    }

    public void FreeTable(Table table)
    {
        if (table != null)
        {
            table.UpdateStatus("Available");
            Console.WriteLine($"Table {table.TableNumber} is now available.");
        }
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        // Sample tables and reservations setup
        var tables = new List<Table>
        {
            new Table(1, 4),
            new Table(2, 2),
            new Table(3, 6)
        };
        var reservations = new List<Reservation>
        {
            new Reservation(101, DateTime.Now.AddMinutes(30), 2, "John Doe")
        };

        var waitstaff = new Waitstaff(tables, reservations);

        // Trigger reservation verification
        waitstaff.VerifyReservation(101);

        // Check table availability
        var availableTable = waitstaff.CheckTableAvailability(2);
        
        // Assign the table and seat the customer
        waitstaff.AssignTable(availableTable);
        waitstaff.SeatCustomer(availableTable);

        // Free up the table after customer finishes dining
        waitstaff.FreeTable(availableTable);
    }
}
