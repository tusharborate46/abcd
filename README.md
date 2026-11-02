
import java.util.*;

public class HospitalScheduling {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter number of patients: ");
        int n = sc.nextInt();

        int at[] = new int[n];
        int bt[] = new int[n];
        int pr[] = new int[n];

        for (int i = 0; i < n; i++) {
            System.out.println("Patient P" + (i + 1));
            System.out.print("Arrival Time: ");
            at[i] = sc.nextInt();
            System.out.print("Treatment Time: ");
            bt[i] = sc.nextInt();
            System.out.print("Priority (1=Critical): ");
            pr[i] = sc.nextInt();
        }

        // Preemptive Priority Scheduling
        int rem[] = bt.clone();
        int ct[] = new int[n];
        int time = 0, done = 0;

        System.out.println("\nPreemptive Gantt Chart:");

        while (done < n) {
            int k = -1;

            for (int i = 0; i < n; i++) {
                if (at[i] <= time && rem[i] > 0 &&
                    (k == -1 || pr[i] < pr[k]))
                    k = i;
            }

            if (k == -1) {
                System.out.print("| Idle ");
                time++;
            } else {
                System.out.print("| P" + (k + 1) + " ");
                rem[k]--;
                time++;

                if (rem[k] == 0) {
                    ct[k] = time;
                    done++;
                }
            }
        }

        System.out.println("|");
        System.out.println("Patient\tWT\tTAT");

        for (int i = 0; i < n; i++)
            System.out.println("P" + (i + 1) + "\t" +
                (ct[i] - at[i] - bt[i]) + "\t" +
                (ct[i] - at[i]));

        // Non-Preemptive Priority Scheduling
        boolean visited[] = new boolean[n];
        int wt[] = new int[n], tat[] = new int[n];
        time = 0;
        done = 0;

        System.out.println("\nNon-Preemptive Gantt Chart:");

        while (done < n) {
            int k = -1;

            for (int i = 0; i < n; i++) {
                if (!visited[i] && at[i] <= time &&
                    (k == -1 || pr[i] < pr[k]))
                    k = i;
            }

            if (k == -1) {
                System.out.print("| Idle ");
                time++;
            } else {
                System.out.print("| P" + (k + 1) + " ");
                wt[k] = time - at[k];
                time += bt[k];
                tat[k] = time - at[k];
                visited[k] = true;
                done++;
            }
        }

        System.out.println("|");
        System.out.println("Patient\tWT\tTAT");

        for (int i = 0; i < n; i++)
            System.out.println("P" + (i + 1) + "\t" +
                wt[i] + "\t" + tat[i]);

        sc.close();
    }
}



import java.util.Scanner;

class Process
{
    Scanner sc = new Scanner(System.in);

    int n; // Total Number of calls
    int Quantum;

    int[] AT = new int[100];
    int[] BT = new int[100];
    int[] TAT = new int[100];
    int[] WT = new int[100];
    int[] CT = new int[100];
    int[] RT = new int[100];

    void input()
    {
        System.out.println("Enter the Total Number of calls");
        n = sc.nextInt();

        for (int i = 0; i < n; i++)
        {
            System.out.println("Enter Arrival Time");
            AT[i] = sc.nextInt();

            System.out.println("Enter Burst Time");
            BT[i] = sc.nextInt();

            RT[i] = BT[i];
        }

        System.out.println("Enter Time Quantum");
        Quantum = sc.nextInt();
    }

    int time = 0;
    int completed = 0;

    void GanttChart()
    {
        int i = 0;

        System.out.println("\nGantt Chart:");

        while (completed < n)
        {
            // If process has arrived and is not completed
            if (AT[i] <= time && RT[i] > 0)
            {
                System.out.print("| C" + (i + 1) + " ");

                // Execute for Time Quantum
                if (RT[i] > Quantum)
                {
                    time = time + Quantum;
                    RT[i] = RT[i] - Quantum;
                }
                else
                {
                    time = time + RT[i];
                    RT[i] = 0;

                    CT[i] = time;
                    completed++;
                }
            }
            else if (time < AT[i])
            {
                // CPU is idle
                time = AT[i];
            }

            // Move to next process
            i++;

            // Start again from C1
            if (i == n)
            {
                i = 0;
            }
        }

        System.out.println("|");

        // Calculate Waiting and Turnaround Time
        for (int j = 0; j < n; j++)
        {
            TAT[j] = CT[j] - AT[j];

            WT[j] = TAT[j] - BT[j];
        }
    }

    void Display_Table()
    {
        System.out.println("\n---------------------------------------------");
        System.out.println("Call\tAT\tBT\tCT\tWT\tTAT");
        System.out.println("---------------------------------------------");

        for (int i = 0; i < n; i++)
        {
            System.out.println(
                "C" + (i + 1) + "\t" +
                AT[i] + "\t" +
                BT[i] + "\t" +
                CT[i] + "\t" +
                WT[i] + "\t" +
                TAT[i]
            );
        }

        System.out.println("---------------------------------------------");
    }
}

class Assignment_6
{
    public static void main(String[] args)
    {
        Process pc = new Process();

        pc.input();
        pc.GanttChart();
        pc.Display_Table();
    }
}








import java.util.*;

public class BankersAlgorithm {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter number of processes: ");
        int n = sc.nextInt();

        System.out.print("Enter number of resources: ");
        int m = sc.nextInt();

        int alloc[][] = new int[n][m];
        int max[][] = new int[n][m];

        int need[][] = new int[n][m]; //remaning needd
        int avail[] = new int[m]; // total resources-allocation
        
        boolean finish[] = new boolean[n];
        int safe[] = new int[n];

        System.out.println("Enter Allocation Matrix:");
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                alloc[i][j] = sc.nextInt();

        System.out.println("Enter Maximum Demand Matrix:");
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                max[i][j] = sc.nextInt();

        System.out.println("Enter Available Resources:");
        for (int j = 0; j < m; j++)
            avail[j] = sc.nextInt();

        // Calculate Need Matrix
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                need[i][j] = max[i][j] - alloc[i][j];

        // Check Safe State
        int count = 0;

        while (count < n) {
            boolean found = false;

            for (int i = 0; i < n; i++) {
                if (!finish[i]) {
                    int j;
                    for (j = 0; j < m; j++)
                        if (need[i][j] > avail[j])
                            break;

                    if (j == m) {
                        for (j = 0; j < m; j++)
                            avail[j] += alloc[i][j];

                        safe[count++] = i;
                        finish[i] = true;
                        found = true;
                    }
                }
            }

            if (!found)
                break;
        }

        if (count == n) {
            System.out.println("System is in SAFE state.");
            System.out.print("Safe Sequence: ");

            for (int i = 0; i < n; i++)
                System.out.print("P" + safe[i] + " ");

        } else {
            System.out.println("System is in UNSAFE state.");
            System.out.println("Deadlock may occur.");
        }

        sc.close();
    }
}




import java.util.Scanner;

public class PageReplacement {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter number of pages: ");
        int n = sc.nextInt();

        if (n < 1 || n > 100) {
            System.out.println("Invalid input!");
            sc.close();
            return;
        }

        int pages[] = new int[n];
                                                    
        System.out.println("Enter page reference string:");
        for (int i = 0; i < n; i++) {
            pages[i] = sc.nextInt();
        }

        System.out.print("Enter number of frames: ");
        int frames = sc.nextInt();

        if (frames < 1 || frames > 100) {
            System.out.println("Invalid input!");
            sc.close();
            return;
        }

        int memory[] = new int[frames];
        int faults = 0;

        // FIFO
        for (int i = 0; i < frames; i++) {
            memory[i] = -1;
        }

        int pointer = 0;

        System.out.println("\n--- FIFO ---");

        for (int i = 0; i < n; i++) {
            boolean found = false;

            for (int j = 0; j < frames; j++) {
                if (memory[j] == pages[i]) {
                    found = true;
                }
            }

            if (found == false) {
                memory[pointer] = pages[i];
                pointer = (pointer + 1) % frames;
                faults++;
                System.out.print("Page " + pages[i] + " -> Fault\t");
            } else {
                System.out.print("Page " + pages[i] + " -> Hit\t");
            }

            for (int j = 0; j < frames; j++) {
                if (memory[j] == -1) {
                    System.out.print("- ");
                } else {
                    System.out.print(memory[j] + " ");
                }
            }
            System.out.println();
        }

        System.out.println("Total Page Faults: " + faults);

        // LRU
        for (int i = 0; i < frames; i++) {
            memory[i] = -1;
        }

        faults = 0;

        System.out.println("\n--- LRU ---");

        for (int i = 0; i < n; i++) {
            boolean found = false;

            for (int j = 0; j < frames; j++) {
                if (memory[j] == pages[i]) {
                    found = true;
                }
            }

            if (found == false) {
                int pos = -1;
                int oldest = n;

                for (int j = 0; j < frames; j++) {
                    if (memory[j] == -1) {
                        pos = j;
                        break;
                    }

                    int k;

                    for (k = i - 1; k >= 0; k--) {
                        if (pages[k] == memory[j]) {
                            break;
                        }
                    }

                    if (k < oldest) {
                        oldest = k;
                        pos = j;
                    }
                }

                memory[pos] = pages[i];
                faults++;

                System.out.print("Page " + pages[i] + " -> Fault\t");
            } else {
                System.out.print("Page " + pages[i] + " -> Hit\t");
            }

            for (int j = 0; j < frames; j++) {
                if (memory[j] == -1) {
                    System.out.print("- ");
                } else {
                    System.out.print(memory[j] + " ");
                }
            }
            System.out.println();
        }

        System.out.println("Total Page Faults: " + faults);

        // OPTIMAL
        for (int i = 0; i < frames; i++) {
            memory[i] = -1;
        }

        faults = 0;

        System.out.println("\n--- OPTIMAL ---");

        for (int i = 0; i < n; i++) {
            boolean found = false;

            for (int j = 0; j < frames; j++) {
                if (memory[j] == pages[i]) {
                    found = true;
                }
            }

            if (found == false) {
                int pos = -1;
                int farthest = -1;

                for (int j = 0; j < frames; j++) {
                    if (memory[j] == -1) {
                        pos = j;
                        break;
                    }

                    int k;

                    for (k = i + 1; k < n; k++) {
                        if (pages[k] == memory[j]) {
                            break;
                        }
                    }

                    if (k > farthest) {
                        farthest = k;
                        pos = j;
                    }
                }

                memory[pos] = pages[i];
                faults++;

                System.out.print("Page " + pages[i] + " -> Fault\t");
            } else {
                System.out.print("Page " + pages[i] + " -> Hit\t");
            }

            for (int j = 0; j < frames; j++) {
                if (memory[j] == -1) {
                    System.out.print("- ");
                } else {
                    System.out.print(memory[j] + " ");
                }
            }
            System.out.println();
        }

        System.out.println("Total Page Faults: " + faults);

        sc.close();
    }
}
