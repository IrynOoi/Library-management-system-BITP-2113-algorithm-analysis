#include <iostream>
#include <fstream>
#include <string>
#include <sstream>
#include <iomanip>
#include <chrono>


using namespace std;


struct LibraryRecord
{
    string StudentID, BookID, IssueDate, DueDate, ReturnDate, ReservationStatus;
    double FineAmount;
    int RenewalCount;

};
string randomData[100];
LibraryRecord record[10000];
LibraryRecord ori_record[10000];

// Function declarations
void ReadRandomData();
void DisplayData(LibraryRecord record[10000]);
void ReadLibraryRecord();

//Sorting relavent function
void ExecuteSorting(int option);
void BubbleSort(int n);
void Merge(int left, int mid, int right);
void MergeSort(int left, int right);
void SortingControl();
void UnsortData();
void Swap(LibraryRecord& x, LibraryRecord& y);


//Searching relevant functions
void ExecuteSearching(int option);
void BubbleSortForBinarySearch(string randomData[], int n);
void BubbleSortForBook(int n);
void SearchingControl();
int LinearSearch(string k);
int ImprovedLinearSearch(string k);
int BinarySearch(string target);
int loopCount;

//Analysis
void  AnalysisControl();
void DisplayTop5HighestFine();
void MergeSortDescending1(int left, int right);
void MergeSortDescending2(int left, int mid, int right);
void summarizeReservationStatus();
void summarizeRenewalCount();




int main()
{
    system("cls");
    ReadRandomData();
    ReadLibraryRecord();


    int option;

    while (true)
    {
        option = 0;

        cout << string(10, '\n');
        cout << string(9, '\t') << "***********************************" << endl;
        cout << string(9, '\t') << "         MAIN MENU OF LIBRARY      " << endl;
        cout << string(9, '\t') << "***********************************" << endl;
        cout << string(9, '\t') << "[1] Display First 100 Data" << endl;
        cout << string(9, '\t') << "[2] Sorting" << endl;
        cout << string(9, '\t') << "[3] Searching" << endl;
        cout << string(9, '\t') << "[4] Analysis" << endl;
        cout << string(9, '\t') << "[5] Exit" << endl;
        cout << "\n\n" << string(9, '\t') << "Please enter your choice (Number 1 - 5 only): ";
        cin >> option;
        cin.clear();
        cin.ignore(10, '\n');
        system("cls");
        cout << string(10, '\n');

        switch (option)
        {
        case 1:
            DisplayData(record);
            cout << "\n\n" << string(9, '\t') << "Please press 'Enter' to continue... ";
            cin.ignore();
            system("cls");
            break;

        case 2:
            SortingControl();
            break;

        case 3:
            SearchingControl();
            break;

        case 4:
            AnalysisControl();
            break;

        case 5:
            cout << string(9, '\t') << "system closed" << endl;
            return 0;
            break;

        default:
            cout << string(9, '\t') << "Please enter the valid choice" << endl;
        }
    }
}

void ReadRandomData()
{
    fstream fin;
    string s;

    fin.open("random_data2.csv", ios::in); // Open the file in read mode

    // Skip the title/header row
    getline(fin, s);

    // Read up to 100 lines of actual data 
    for (int i = 0; i < 9999 && getline(fin, s); i++)
    {
        randomData[i] = s; // Store the string directly into the array
    }

    fin.close(); // Close the file

}
void DisplayData(LibraryRecord record[10000])
{
    cout << string(5, '\t') << "=========================================================================================================" << endl;
    cout << string(5, '\t') << "|                                              Library Record Data                                      |" << endl;
    cout << string(5, '\t') << "=========================================================================================================" << endl;
    cout << string(5, '\t');
    cout << "|" << left << setw(10) << "StudentID";
    cout << "|" << left << setw(10) << "IssueDate";
    cout << "|" << left << setw(10) << "DueDate";
    cout << "|" << left << setw(10) << "ReturnDate";
    cout << "|" << left << setw(10) << "FineAmount";
    cout << "|" << left << setw(15) << "Renewal Count";
    cout << "|" << left << setw(20) << "Reservation Status";
    cout << "|" << left << setw(11) << "BookID" << "|" << endl;
    cout << string(5, '\t') << "=========================================================================================================" << endl;

    for (int i = 0; i < 100; i++) // Loop through the first 100 data
    {
        // Check if the record contains valid data
        if (!record[i].StudentID.empty()) // Assuming StudentID is a string
        {
            cout << string(5, '\t');
            cout << "|" << left << setw(10) << record[i].StudentID;
            cout << "|" << left << setw(10) << record[i].IssueDate;
            cout << "|" << left << setw(10) << record[i].DueDate;
            cout << "|" << left << setw(10) << record[i].ReturnDate;
            cout << "|" << left << setw(10) << record[i].FineAmount;
            cout << "|" << left << setw(15) << record[i].RenewalCount;
            cout << "|" << left << setw(20) << record[i].ReservationStatus;
            cout << "|" << left << setw(11) << record[i].BookID << "|" << endl;
        }
    }
    cout << string(5, '\t') << "=========================================================================================================" << endl;
}



void ReadLibraryRecord()
{
    fstream fin;

    fin.open("library_data.csv", ios::in);

    if (!fin.is_open())
    {
        cout << "Error: Could not open file 'library_data.csv'" << endl;
        return;
    }

    string line;
    string fields[8];

    // Skip the header line
    getline(fin, line);

    int i = 0;
    try
    {
        while (getline(fin, line) && i < 10000) // Limit to 10,000 records
        {
            // Clear the fields array
            for (int j = 0; j < 8; j++)
            {
                fields[j] = "";
            }

            stringstream s(line);
            string word;
            int fieldCount = 0;

            // Parse each field in the row
            while (getline(s, word, ',') && fieldCount < 8)
            {
                fields[fieldCount++] = word;
            }

            // Check if the row has enough fields to avoid out-of-range errors
            if (fieldCount < 8)
            {
                cout << "Error: Incomplete data in row " << i + 1 << endl;
                continue;
            }

            // Assign fields to the struct
            ori_record[i].StudentID = fields[0];
            ori_record[i].IssueDate = fields[1];
            ori_record[i].DueDate = fields[2];
            ori_record[i].ReturnDate = fields[3];
            ori_record[i].FineAmount = stod(fields[4]); // Convert to double
            ori_record[i].RenewalCount = stoi(fields[5]); // Convert to int
            ori_record[i].ReservationStatus = fields[6];
            ori_record[i].BookID = fields[7];

            i++;
        }

        // Copy parsed records
        for (int j = 0; j < i; j++)
        {
            record[j] = ori_record[j];
        }
    }
    catch (const exception& e)
    {
        cout << "Error: " << e.what() << endl;
        cout << "Failed at record index: " << i << endl;
    }

    fin.close();
}



void Swap(LibraryRecord& x, LibraryRecord& y)
{
    LibraryRecord  temp = x;
    x = y;
    y = temp;
    loopCount++;
}

void SortingControl()
{
    system("cls");
    cout << string(10, '\n');

    int option;
    cout << string(9, '\t') << "****************" << endl;
    cout << string(9, '\t') << "  SORTING MENU  " << endl;
    cout << string(9, '\t') << "****************" << endl;
    cout << string(9, '\t') << "[1] Bubble Sort" << endl;
    cout << string(9, '\t') << "[2] Merge Sort" << endl;
    cout << string(9, '\t') << "[3] Unsort" << endl;
    cout << string(9, '\t') << "[4] Back" << endl;

    cout << "\n\n" << string(9, '\t') << "Please enter your choice (Number 1 - 3 only): ";

    while (true)
    {

        cin >> option;
        cin.clear();
        cin.ignore(10, '\n');
        system("cls");
        cout << string(10, '\n');

        switch (option)
        {
        case 1:
        case 2:
            ExecuteSorting(option);
            break;

        case 3:
            UnsortData();
            break;

        case 4:
            system("cls");
            main();

        default:
            cout << string(9, '\t') << "Please enter the valid choice" << endl;
        }
    }
}

void ExecuteSorting(int option)
{
    copy_n(ori_record, 10000, record);
    // Initialize a variable 'loopCount' to keep track of the number of loops
    loopCount = 0;

    // Create a variable 'elapsed_seconds' to store the time duration of the sorting process.
    chrono::duration<double> elapsed_seconds;

    // Print a message to let the user know that the program is processing and the user should wait.
    cout << string(9, '\t') << "Please wait for a while..." << endl;

    // Check if the user has chosen the option to perform BubbleSort (assuming 'option' is provided earlier in the code).
    if (option == 1)
    {
        // Record the start time of the BubbleSort operation using the system clock.
        auto start = chrono::system_clock::now();

        // Call the BubbleSort function with an array of size 10,000 (assuming BubbleSort takes care of sorting the array).
        BubbleSort(10000);

        // Record the end time after the sorting operation completes.
        auto end = chrono::system_clock::now();

        // Calculate the elapsed time between the start and end using the difference between the two times.
        elapsed_seconds = end - start;


    }
    else  // If the user didn't choose option 1, assume they want to run MergeSort instead.
    {
        // Record the start time of the MergeSort operation using the system clock.
        auto start = chrono::system_clock::now();

        // Call the MergeSort function with the array indices to sort (from index 0 to index 9999).
        MergeSort(0, 9999);

        // Record the end time after the sorting operation completes.
        auto end = chrono::system_clock::now();

        // Calculate the elapsed time between the start and end using the difference between the two times.
        elapsed_seconds = end - start;

    }


    system("cls");
    cout << string(10, '\n');
    try
    {
        DisplayData(record);
    }
    catch (const exception& e)
    {
        cerr << "Error in DisplayData: " << e.what() << endl;
        throw;
    }

    cout << "\n\n" << string(9, '\t') << "Total numbers of swaps: " << loopCount << endl;
    cout << string(9, '\t') << "The duration time is " << elapsed_seconds.count() << " second" << endl;
    cout << "\n\n" << string(9, '\t') << "Please press 'Enter' to continue...";

    system("pause");
    SortingControl();
}
// Swap function definition
template <typename T>
void Swap(T& a, T& b)
{
    T temp = a;
    a = b;
    b = temp;
}

void BubbleSort(int n)
{
    for (int i = 0; i < n - 1; i++)
    {
        for (int k = 0; k < n - i - 1; k++)
        {
            if (record[k].StudentID > record[k + 1].StudentID)
            {
                Swap(record[k], record[k + 1]);
            }
        }
    }
}
void BubbleSortForBook(int n)
{
    for (int i = 0; i < n - 1; i++)
    {
        for (int k = 0; k < n - i - 1; k++)
        {
            if (record[k].BookID > record[k + 1].BookID)
            {
                swap(record[k], record[k + 1]);
            }
        }
    }
}


void MergeSort(int left, int right)
{
    if (left < right)
    {
        int mid = left + (right - left) / 2;

        MergeSort(left, mid);
        MergeSort(mid + 1, right);

        Merge(left, mid, right);
    }
}

void BubbleSortForBinarySearch(string randomData[], int n)
{
    for (int i = 0; i < n - 1; i++)
    {
        for (int k = 0; k < n - i - 1; k++)
        {
            if (randomData[k] > randomData[k + 1]) // Compare strings lexicographically
            {
                swap(randomData[k], randomData[k + 1]); // Use built-in swap for strings
            }
        }
    }
}




void Merge(int left, int mid, int right)
{
    int n1 = mid - left + 1;
    int n2 = right - mid;

    // Create temporary arrays
    LibraryRecord* leftArray = new LibraryRecord[n1];
    LibraryRecord* rightArray = new LibraryRecord[n2];

    // Copy data to temp arrays
    for (int i = 0; i < n1; i++) {
        leftArray[i] = record[left + i];
    }
    for (int j = 0; j < n2; j++) {
        rightArray[j] = record[mid + 1 + j];
    }

    // Merge the temp arrays back into record[left...right]
    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (leftArray[i].StudentID <= rightArray[j].StudentID) {
            record[k] = leftArray[i];
            i++;
        }
        else {
            record[k] = rightArray[j];
            j++;
        }
        k++;
        loopCount++;
    }

    // Copy remaining elements of leftArray
    while (i < n1) {
        record[k] = leftArray[i];
        i++;
        k++;
    }

    // Copy remaining elements of rightArray
    while (j < n2) {
        record[k] = rightArray[j];
        j++;
        k++;
    }

    // Free dynamically allocated memory
    delete[] leftArray;
    delete[] rightArray;
}



void UnsortData() // Function to reset the `record` array back to its original unsorted state
{
    // Copy the first 10000 elements from the 'ori_record' array to the 'record' array
    // 'ori_record' is assumed to store the original unsorted data, while 'record' holds the current data.
    copy_n(ori_record, 10000, record);

    // Display the updated (unsorted) 'record' array on the screen
    DisplayData(record);
    cout << "\n\n" << string(9, '\t') << "Data Unsorted. Please press 'Enter' to continue..." << endl;
    cout << "\n\n" << string(9, '\t');

    system("pause");
    SortingControl();
}

void SearchingControl()
{
    int option;

    while (true)
    {
        cout << string(9, '\t') << "***********************************" << endl;
        cout << string(9, '\t') << "          LIBRARY DATA SYSTEM      " << endl;
        cout << string(9, '\t') << "***********************************" << endl;
        cout << string(9, '\t') << "[1] Linear Search 100 Random Data" << endl;
        cout << string(9, '\t') << "[2] Binary Search 100 Random Data" << endl;
        cout << string(9, '\t') << "[3] Improved Linear Search 100 Random Data" << endl;
        cout << string(9, '\t') << "[4] Back" << endl;

        cout << "\n\n" << string(9, '\t') << "Please enter your choice (Number 1 - 4 only): ";
        cin >> option;
        cin.clear();
        cin.ignore(10, '\n');
        system("cls");
        cout << string(10, '\n');

        switch (option)
        {
        case 1:
        case 2:
        case 3:
            ExecuteSearching(option);
            break;

        case 4:
            system("cls");
            main();


        default:
            cout << string(9, '\t') << "Please enter the valid choice" << endl;
        }
    }
}



void ExecuteSearching(int option)
{
    int index;
    string info[100][2];
    chrono::duration<double> elapsed_seconds;
    // Print a message to let the user know that the program is processing and the user should wait.
    cout << string(9, '\t') << "Please wait for a while..." << endl;
    if (option == 1)
    {
        auto start = chrono::system_clock::now();


        for (int i = 0; i < 100; i++)
        {
            index = LinearSearch(randomData[i]);
            info[i][0] = randomData[i];
            info[i][1] = (index == -1) ? "Not Found" : to_string(index); // Convert index to string
        }

        auto end = chrono::system_clock::now();
        elapsed_seconds = end - start;
    }
    else if (option == 2)
    {
        BubbleSortForBinarySearch(randomData, 100);
        BubbleSortForBook(10000);
        auto start = chrono::system_clock::now();

        for (int i = 0; i < 100; i++)
        {



            index = BinarySearch(randomData[i]);
            info[i][0] = randomData[i];
            info[i][1] = (index == -1) ? "Not Found" : to_string(index);

        }

        auto end = chrono::system_clock::now();
        elapsed_seconds = end - start;
    }
    else
    {
        auto start = chrono::system_clock::now();

        for (int i = 0; i < 100; i++)
        {

            index = ImprovedLinearSearch(randomData[i]);
            info[i][0] = randomData[i];
            info[i][1] = (index == -1) ? "Not Found" : to_string(index);

        }

        auto end = chrono::system_clock::now();
        elapsed_seconds = end - start;
    }
    system("cls");
    cout << string(9, '\t') << "=====================================" << endl;
    cout << string(9, '\t') << "|         BOOK   DATA               |" << endl;
    cout << string(9, '\t') << "=====================================" << endl;
    cout << string(9, '\t');
    printf("|%10s|%18s|%5s|", "BookID", "Is Found/Not Found", "Index");
    cout << "\n" << string(9, '\t') << "====================================" << endl;
    for (int i = 0; i < 100; i++)
    {
        cout << string(9, '\t');
        cout << "|" << left << setw(10) << info[i][0];
        if (info[i][1] == "Not Found") // Compare with "Not Found"
        {
            cout << "|" << left << setw(18) << "Not Found";
            cout << "|" << left << setw(5) << "  -  " << "|";
        }
        else
        {
            cout << "|" << left << setw(18) << "Is Found";
            cout << "|" << left << setw(5) << info[i][1] << "|";
        }
        cout << endl;
    }
    cout << string(9, '\t') << "=====================================" << endl;

    cout << "\n" << string(9, '\t') << "The duration searching time is " << elapsed_seconds.count() * 1000000 << " microseconds" << endl;
    cout << "\n\n" << string(9, '\t') << "Please press 'Enter' to continue...";
    cin.ignore();
    system("cls");
    cout << string(10, '\n');
}

int BinarySearch(string target)
{
    int first = 0;
    int last = 9999;

    while (first <= last)
    {
        int mid = (first + last) / 2;

        if (target > record[mid].BookID)
        {
            first = mid + 1;
        }
        else if (target < record[mid].BookID)
        {
            last = mid - 1;
        }
        else
        {
            //target found
            return mid;
        }
    }

    //target not found
    return -1;
}


// Function to perform linear search
int LinearSearch(string k)
{
    int i;
    // Iterate through the record array
    for (i = 0; i < 10000; i++)
    {
        if (k == record[i].BookID) // Check if the  BookID in randomData array matches the BookID in record array
        {
            return i;             // Return index if found
        }
    }

    return -1; // Return -1 if not found
}

int ImprovedLinearSearch(string k)
{
    int left = 0;
    int right = 9999;
    while (left <= right)
    {
        if (k == record[left].BookID)
        {
            return left;
        }
        if (k == record[right].BookID)
        {
            return right;
        }
        left++;
        right--;
    }
    return -1;
}

// Merge function to merge two sorted subarrays
void MergeSortDescending2(int left, int mid, int right)
{
    int n1 = mid - left + 1; // Size of left subarray
    int n2 = right - mid;    // Size of right subarray

    // Create temporary arrays
   // Create temporary arrays
    LibraryRecord* leftArray = new LibraryRecord[n1];
    LibraryRecord* rightArray = new LibraryRecord[n2];

    // Copy data to temporary arrays
    for (int i = 0; i < n1; i++)
        leftArray[i] = record[left + i];
    for (int j = 0; j < n2; j++)
        rightArray[j] = record[mid + 1 + j];

    int i = 0, j = 0, k = left;

    // Merge the temporary arrays back into `record[left...right]`
    while (i < n1 && j < n2)
    {
        // Compare `FineAmount` in descending order
        if (leftArray[i].FineAmount >= rightArray[j].FineAmount)
        {
            record[k] = leftArray[i];
            i++;
        }
        else
        {
            record[k] = rightArray[j];
            j++;
        }
        k++;
    }

    // Copy remaining elements of leftArray
    while (i < n1)
    {
        record[k] = leftArray[i];
        i++;
        k++;
    }

    // Copy remaining elements of rightArray
    while (j < n2)
    {
        record[k] = rightArray[j];
        j++;
        k++;
    }

    // Free dynamically allocated memory
    delete[] leftArray;
    delete[] rightArray;
}

// Merge sort function
void MergeSortDescending1(int left, int right)
{
    if (left < right)
    {
        int mid = left + (right - left) / 2;

        // Recursively sort first and second halves
        MergeSortDescending1(left, mid);
        MergeSortDescending1(mid + 1, right);

        // Merge the sorted halves
        MergeSortDescending2(left, mid, right);
    }
}


void AnalysisControl()
{
    system("cls");
    int option;
    cout << string(9, '\t') << "===================================" << endl;
    cout << string(9, '\t') << "|          ANALYSIS MENU          |" << endl;
    cout << string(9, '\t') << "===================================" << endl;
    cout << string(9, '\t') << "[1] Display Top 5 Highest Fine" << endl;
    cout << string(9, '\t') << "[2] Display the summary of count of reservation" << endl;
    cout << string(9, '\t') << "[3] Display  the summary of count of renewal" << endl;
    cout << string(9, '\t') << "[4] Back" << endl;

    cout << "\n\n" << string(9, '\t') << "Please enter your choice (Number 1 - 4 only): ";

    while (true)
    {
        cin >> option;
        cin.clear();
        cin.ignore(10, '\n');
        system("cls");
        cout << string(10, '\n');

        switch (option)
        {
        case 1:
            DisplayTop5HighestFine();
            break;

        case 2:
            summarizeReservationStatus();
            break;
        case 3:
            summarizeRenewalCount();
            break;


        case 4:
            system("cls");
            main();

        default:
            cout << string(9, '\t') << "Please enter the valid choice" << endl;
        }
    }
}

// Function to display top 5 highest fines and include calculations
void DisplayTop5HighestFine()
{

    // Sort records by fine amount in descending order
    MergeSortDescending1(0, 9999);

    // Check if there are any records with fines
    bool hasRecordsWithFines = false;
    for (int i = 0; i < 9999; i++)
    {
        if (record[i].FineAmount > 0)
        {
            hasRecordsWithFines = true;
            break;
        }
    }

    if (!hasRecordsWithFines)
    {
        cout << string(9, '\t') << "No records with fines found." << endl;
        cout << "\nPress Enter to continue...";
        cin.ignore();
        return;
    }

    system("cls");
    cout << string(9, '\t') << "========================================================" << endl;
    cout << string(9, '\t') << "|         TOP 5 STUDENTS WITH HIGHEST FINES            |" << endl;
    cout << string(9, '\t') << "========================================================" << endl;
    cout << string(9, '\t');
    printf("|%10s|%10s|%10s|%10s|%10s|", "StudentID", "BookID", "Fine", "Due Date", "Return Date");
    cout << "\n" << string(9, '\t') << "========================================================" << endl;

    int count = 0;  // Counter for displayed records
    double totalFine = 0;  // Variable to store total fine amount
    for (int i = 0; i < 9999 && count < 5; i++)
    {
        if (record[i].FineAmount > 0)
        {
            cout << string(9, '\t');
            cout << "|" << left << setw(10) << record[i].StudentID
                << "|" << left << setw(10) << record[i].BookID
                << "|" << left << setw(10) << record[i].FineAmount
                << "|" << left << setw(10) << record[i].DueDate
                << "|" << left << setw(10) << record[i].ReturnDate << "|";
            cout << endl;

            // Add the fine amount to the total fine
            totalFine += record[i].FineAmount;
            count++;
        }
    }

    // Calculate average fine
    double averageFine = (count > 0) ? (totalFine / count) : 0;

    // Display total and average fines
    cout << string(9, '\t') << "========================================================" << endl;
    cout << string(9, '\t') << "| Total Fine: " << setw(41) << totalFine << "|" << endl;
    cout << string(9, '\t') << "| Average Fine: " << setw(39) << averageFine << "|" << endl;
    cout << string(9, '\t') << "========================================================" << endl;

    cout << "\n" << string(9, '\t');

    system("pause");
    AnalysisControl();
    cout << string(10, '\n');
}
void DisplayReservationCount()
{
    // Track students who have been processed
    bool processed[9999] = { false };

    system("cls");
    cout << string(9, '\t') << "========================================================" << endl;
    cout << string(9, '\t') << "|              STUDENT RESERVATION COUNTS              |" << endl;
    cout << string(9, '\t') << "========================================================" << endl;
    cout << string(9, '\t');
    printf("|%10s|%10s|%10s|\n", "StudentID", "Reservations", "Total");
    cout << string(9, '\t') << "========================================================" << endl;

    for (int i = 0; i < 9999; i++) {
        // If this student has not been processed yet
        if (!processed[i] && record[i].StudentID != "") {
            int reservationCount = 0;

            // Count the reservations for this student
            for (int j = 0; j < 9999; j++) {
                if (record[j].StudentID == record[i].StudentID && record[j].ReservationStatus == "Reserved") {
                    reservationCount++;
                }
            }

            // Display the reservation count for this student
            cout << string(9, '\t');
            cout << "|" << left << setw(10) << record[i].StudentID
                << "|" << left << setw(10) << reservationCount
                << "|" << left << setw(10) << reservationCount << "|";
            cout << endl;

            // Mark this student as processed
            processed[i] = true;
        }
    }

    cout << string(9, '\t') << "========================================================" << endl;
    cout << "\n" << string(9, '\t') << "Press 'Enter' to continue...";

    system("pause");
    AnalysisControl();
}


void summarizeReservationStatus()
{
    // Initialize counters for each status
    int reservedCount = 0;
    int availableCount = 0;
    int notReservedCount = 0;
    int waitingCount = 0;

    // Loop through each record to count the ReservationStatus
    for (int i = 0; i < 10000; i++) {
        // Skip records with empty StudentID or BookID (to avoid processing invalid entries)
        if (record[i].StudentID.empty() || record[i].BookID.empty()) {
            continue;
        }

        // Check the ReservationStatus and update the counters
        if (record[i].ReservationStatus == "Reserved") {
            reservedCount++;
        }
        else if (record[i].ReservationStatus == "Available") {
            availableCount++;
        }
        else if (record[i].ReservationStatus == "Not Reserved") {
            notReservedCount++;
        }
        else if (record[i].ReservationStatus == "Waiting") {
            waitingCount++;
        }
    }

    // Output the results in a table format
    system("cls");
    cout << string(9, '\t') << "=================================" << endl;
    cout << string(9, '\t') << "|   Reservation Status Summary  |" << endl;
    cout << string(9, '\t') << "=================================" << endl;
    cout << string(9, '\t');
    printf("|%20s|%10s|\n", "Reservation Status", "Count");
    cout << string(9, '\t') << "=================================" << endl;

    // Print the status counts in a tabular form
    cout << string(9, '\t');
    printf("|%20s|%10d|\n", "Reserved", reservedCount);
    cout << string(9, '\t');
    printf("|%20s|%10d|\n", "Available", availableCount);
    cout << string(9, '\t');
    printf("|%20s|%10d|\n", "Not Reserved", notReservedCount);
    cout << string(9, '\t');
    printf("|%20s|%10d|\n", "Waiting", waitingCount);

    cout << string(9, '\t') << "=================================" << endl;
    cout << "\n" << string(9, '\t') << "Press 'Enter' to continue...";

    cout << string(10, '\n');

    system("pause");
    AnalysisControl();

}

void summarizeRenewalCount()
{
    // Initialize counters for each renewal count
    int renewalCount0 = 0;
    int renewalCount1 = 0;
    int renewalCount2 = 0;
    int renewalCount3 = 0;

    // Loop through each record to count students based on RenewalCount
    for (int i = 0; i < 10000; i++) {
        // Skip records with empty StudentID or BookID (to avoid processing invalid entries)
        if (record[i].StudentID.empty() || record[i].BookID.empty()) {
            continue;
        }

        // Check the RenewalCount and update the counters
        if (record[i].RenewalCount == 0) {
            renewalCount0++;
        }
        else if (record[i].RenewalCount == 1) {
            renewalCount1++;
        }
        else if (record[i].RenewalCount == 2) {
            renewalCount2++;
        }
        else if (record[i].RenewalCount == 3) {
            renewalCount3++;
        }
    }

    // Output the results in a table format
    system("cls");
    cout << string(9, '\t') << "=================================" << endl;
    cout << string(9, '\t') << "|   Renewal Count Summary      |" << endl;
    cout << string(9, '\t') << "=================================" << endl;
    cout << string(9, '\t');
    printf("|%20s|%10s|\n", "Renewal Count", "Count");
    cout << string(9, '\t') << "=================================" << endl;

    // Print the renewal count summaries in a tabular form
    cout << string(9, '\t');
    printf("|%20d|%10d|\n", 0, renewalCount0);
    cout << string(9, '\t');
    printf("|%20d|%10d|\n", 1, renewalCount1);
    cout << string(9, '\t');
    printf("|%20d|%10d|\n", 2, renewalCount2);
    cout << string(9, '\t');
    printf("|%20d|%10d|\n", 3, renewalCount3);

    cout << string(9, '\t') << "=================================" << endl;
    cout << "\n" << string(9, '\t') << "Press 'Enter' to continue...";
    system("pause");
    AnalysisControl();

}