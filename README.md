# Experiment-5-Creation-of-two-datacenters-with-one-host-each-and-run-two-cloudlets-on-them.-

 
---

## Overview
This project demonstrates a CloudSim 3.0.3 simulation where two users submit cloudlets that are executed across two separate datacenters. Each datacenter contains one host, and tasks are scheduled through a broker that distributes workloads to virtual machines.

This experiment illustrates multi-datacenter architecture, user-level task scheduling, and distributed cloud execution behavior.

---

## Aim
To create two datacenters with one host each and execute cloudlets from two users to analyze distributed execution behavior.

---

## Objectives
- Simulate multiple datacenters in CloudSim
- Configure hosts and allocate resources
- Simulate multiple users submitting tasks
- Create virtual machines for execution
- Analyze distributed execution results

---

## Tools and Technologies

| Tool | Version |
|------|---------|
| Java JDK | 1.8+ |
| CloudSim | 3.0.3 |
| IDE | IntelliJ / Eclipse |
| OS | Windows / Linux |

---

## System Configuration

### Datacenter Configuration

| Parameter | Value |
|----------|------|
| Datacenters | 2 |
| Hosts per DC | 1 |
| RAM | 2048 MB |
| Storage | 1,000,000 MB |
| Bandwidth | 10,000 Mbps |
| Scheduler | Time Shared |

---

### Virtual Machine Configuration

| Parameter | VM1 | VM2 |
|----------|-----|-----|
| MIPS | 500 | 500 |
| RAM | 512 MB | 512 MB |
| BW | 1000 Mbps | 1000 Mbps |
| VMM | Xen | Xen |
| Scheduler | Time Shared | Time Shared |

---

### Cloudlet Configuration

| Parameter | Value |
|----------|------|
| Users | 2 |
| Cloudlets | 4 |
| Lengths | 20000, 40000, 60000, 80000 |
| File Size | 300 MB |
| Output Size | 300 MB |
| PEs | 1 |

---
 
---

## Algorithm
1. Initialize CloudSim library  
2. Create two datacenters  
3. Configure one host per datacenter  
4. Create broker  
5. Create virtual machines  
6. Submit VM list to broker  
7. Create cloudlets with different lengths  
8. Submit cloudlets  
9. Start simulation  
10. Collect results  

---

## Execution Time Formula
Execution Time = Cloudlet Length / VM MIPS

## Program 
import org.cloudbus.cloudsim.*;
import org.cloudbus.cloudsim.core.CloudSim;
import org.cloudbus.cloudsim.provisioners.*;
import java.util.*;

public class TwoUserSimulation {

    static Datacenter createDatacenter(String name) {

        List<Host> hostList = new ArrayList<>();

        List<Pe> peList = new ArrayList<>();
        peList.add(new Pe(0,new PeProvisionerSimple(1000)));

        Host host = new Host(
                0,
                new RamProvisionerSimple(2048),
                new BwProvisionerSimple(10000),
                1000000,
                peList,
                new VmSchedulerTimeShared(peList)
        );

        hostList.add(host);

        DatacenterCharacteristics characteristics =
                new DatacenterCharacteristics(
                        "x86","Linux","Xen",
                        hostList,
                        10.0,3.0,0.05,0.001,0.0
                );

        try {
            return new Datacenter(
                    name,
                    characteristics,
                    new VmAllocationPolicySimple(hostList),
                    new LinkedList<Storage>(),
                    0
            );
        }
        catch(Exception e){
            e.printStackTrace();
            return null;
        }
    }

    public static void main(String[] args) {

        try {

            CloudSim.init(2, Calendar.getInstance(), false);

            Datacenter dc1 = createDatacenter("Datacenter_1");
            Datacenter dc2 = createDatacenter("Datacenter_2");

            // USER 1
            DatacenterBroker broker1 = new DatacenterBroker("User1");
            int id1 = broker1.getId();

            Vm vm1 = new Vm(0,id1,500,1,512,1000,10000,"Xen",
                    new CloudletSchedulerTimeShared());

            broker1.submitVmList(Arrays.asList(vm1));

            UtilizationModel model = new UtilizationModelFull();

            Cloudlet c1 = new Cloudlet(0,20000,1,300,300,model,model,model);
            Cloudlet c2 = new Cloudlet(1,40000,1,300,300,model,model,model);

            c1.setUserId(id1);
            c2.setUserId(id1);

            broker1.submitCloudletList(Arrays.asList(c1,c2));


            // USER 2
            DatacenterBroker broker2 = new DatacenterBroker("User2");
            int id2 = broker2.getId();

            Vm vm2 = new Vm(1,id2,500,1,512,1000,10000,"Xen",
                    new CloudletSchedulerTimeShared());

            broker2.submitVmList(Arrays.asList(vm2));

            Cloudlet c3 = new Cloudlet(2,30000,1,300,300,model,model,model);
            Cloudlet c4 = new Cloudlet(3,60000,1,300,300,model,model,model);

            c3.setUserId(id2);
            c4.setUserId(id2);

            broker2.submitCloudletList(Arrays.asList(c3,c4));


            CloudSim.startSimulation();

            List<Cloudlet> r1 = broker1.getCloudletReceivedList();
            List<Cloudlet> r2 = broker2.getCloudletReceivedList();

            CloudSim.stopSimulation();


            System.out.println("\nUSER 1 RESULTS:");
            for(Cloudlet cl : r1)
                System.out.println("Cloudlet "+cl.getCloudletId()+" Time "+cl.getActualCPUTime());

            System.out.println("\nUSER 2 RESULTS:");
            for(Cloudlet cl : r2)
                System.out.println("Cloudlet "+cl.getCloudletId()+" Time "+cl.getActualCPUTime());

        }
        catch(Exception e){
            e.printStackTrace();
        }
    }
}

 

## Sample Output
========== RESULT ==========
Cloudlet 0 | Length: 20000 | VM: 0 | Datacenter: 2 | Time: 40.0
Cloudlet 1 | Length: 40000 | VM: 1 | Datacenter: 3 | Time: 80.0
Cloudlet 2 | Length: 60000 | VM: 0 | Datacenter: 2 | Time: 120.0
Cloudlet 3 | Length: 80000 | VM: 1 | Datacenter: 3 | Time: 160.0

 ## Output
========== RESULT ==========
<img width="354" height="205" alt="image" src="https://github.com/user-attachments/assets/27e89932-037d-4c81-a43b-8b6a58271540" />



---

## Result
The simulation successfully executed tasks submitted by multiple users across two datacenters. Cloudlets were scheduled based on available resources and executed accordingly.

---

## Conclusion
This experiment demonstrates that CloudSim effectively models distributed cloud environments. It shows how workloads are handled across multiple datacenters and validates distributed task execution concepts used in real cloud infrastructure.
