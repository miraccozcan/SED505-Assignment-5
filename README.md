# Multimodal Input for Autonomous Driving System

## 1. Introduction
The use of multimodal input in machine learning is becoming more popular due to its ability to enhance performance and robustness by integrating data from various modalities, such as text, images, audio, video, and sensor data.  
In autonomous vehicles, multimodal input plays a critical role in tasks like:
- **Object detection**
- **Route planning**
- **Real-time decision-making**

This project aims to:
1. Design a UML diagram for a multimodal input design pattern.
2. Implement an Autonomous Driving System using multimodal inputs.
3. Provide a simulation output showcasing the system’s functionality.
4. Compare machine learning design patterns with standard design patterns.

---

## 2. UML Diagram
A Unified Modeling Language (UML) diagram visually represents the integration of the following components:

### Components:
- **Sensors**:
  - `CameraData`
  - `LidarData`
  - `GPSData`
- **Processing Modules**:
  - `PerceptionModule`
  - `PlanningModule`
  - `ControlModule`
- **Outputs**:
  - Navigation decisions like "turn left," "stop," or "accelerate."

### UML Diagram Relationships:
- **Actors**:
  - Sensors (`CameraData`, `LidarData`, `GPSData`).
- **Relationships**:
  - **Solid lines** for associations (e.g., between modules and sensors).
  - **Dashed lines** for dependencies (e.g., one module relies on another module’s output).
  - **Aggregation** to represent parts of a whole (e.g., `ControlModule` aggregates `PerceptionModule` and `PlanningModule`).
## UML Diagram
![UML Diagram](

---

## 3. Common Usage: Multimodal Input
### Applications of Multimodal Input in Various Industries:
1. **Multimedia Analysis**:
   - Used in video summarization, image captioning, and object recognition by combining visual and textual data.
2. **Natural Language Processing (NLP)**:
   - Enhances sentiment analysis by integrating text and images.
3. **Human-Computer Interaction (HCI)**:
   - Enables voice assistants and gesture recognition systems to interpret visual and auditory cues.
4. **Autonomous Vehicles**:
   - Utilizes sensors such as cameras, LIDAR, and GPS for object detection and navigation.
5. **Healthcare**:
   - Combines medical images, electronic health records (EHR), and clinical notes for diagnosis and treatment planning.
6. **Robotics**:
   - Uses multimodal inputs to interact with the environment.
7. **Augmented and Virtual Reality (AR/VR)**:
   - Enhances user experiences by integrating visual, auditory, and haptic data.
8. **Surveillance and Security**:
   - Enables event detection and anomaly identification through video and audio data integration.

---

## 4. Autonomous Driving System
### Objective
Develop an autonomous driving system that integrates data from:
1. **Cameras**: For object detection.
2. **LIDAR**: For obstacle detection and spatial awareness.
3. **GPS**: For navigation and destination tracking.

### Components:
1. **Sensor Data Structures**:
   - `CameraData`: Captures objects like pedestrians and vehicles.
   - `LidarData`: Detects road curvature and obstacles.
   - `GPSData`: Tracks current and destination coordinates.
2. **Modules**:
   - **PerceptionModule**:
     - Processes data from sensors.
     - Updates object detection and obstacle information hourly.
   - **PlanningModule**:
     - Plans navigation routes.
     - Adjusts routes based on sensor data.
   - **ControlModule**:
     - Combines outputs from Perception and Planning modules.
     - Controls speed and direction.
3. **Simulation Logic**:
   - **Duration**: Runs for 24 hours (or until the destination is within 25 km).
   - **Outputs**:
     - Hourly updates on the vehicle’s position.
     - Actions taken to navigate closer to the destination.

---

## 5. Implementation

### Code in C++

```
#include <iostream>
#include <cmath>

// Constants
const double EARTH_CIRCUMFERENCE = 40040.00;

// Sensor Data Structures
struct CameraData {
    enum class ObjectType { NONE, VEHICLE, PEDESTRIAN, BICYCLE, STOPLIGHT, SPEEDLIMIT };
    ObjectType object = ObjectType::NONE;
};

struct LidarData {
    enum class ObjectType { ROAD_CURVATURE, SMALL_OBSTRUCTION, LARGE_OBSTRUCTION };
    ObjectType object = ObjectType::ROAD_CURVATURE;
};

struct GPSData {
    double curr_longitude;
    double curr_latitude;
    double dest_longitude;
    double dest_latitude;
};

// Perception Module
class PerceptionModule {
    int timer = 0;
    const int MAX_TIME = 6;

public:
    void processCameraData(CameraData &cameraData) {
        switch (timer % MAX_TIME) {
            case 0: cameraData.object = CameraData::ObjectType::NONE; break;
            case 1: cameraData.object = CameraData::ObjectType::VEHICLE; break;
            case 2: cameraData.object = CameraData::ObjectType::PEDESTRIAN; break;
            case 3: cameraData.object = CameraData::ObjectType::BICYCLE; break;
            case 4: cameraData.object = CameraData::ObjectType::STOPLIGHT; break;
            case 5: cameraData.object = CameraData::ObjectType::SPEEDLIMIT; break;
        }
    }

    void processLidarData(LidarData &lidarData) {
        switch (timer % MAX_TIME) {
            case 0:
            case 3: lidarData.object = LidarData::ObjectType::ROAD_CURVATURE; break;
            case 1:
            case 4: lidarData.object = LidarData::ObjectType::SMALL_OBSTRUCTION; break;
            case 2:
            case 5: lidarData.object = LidarData::ObjectType::LARGE_OBSTRUCTION; break;
        }
    }

    void processGPSData(GPSData &gpsData, double speed, double direction) {
        gpsData.curr_longitude += 180.0 * speed * sin(M_PI / 180.0 * direction) / EARTH_CIRCUMFERENCE;
        gpsData.curr_latitude += 180.0 * speed * cos(M_PI / 180.0 * direction) / EARTH_CIRCUMFERENCE;
    }

    void incrementTime() { timer++; }
};

// Planning Module
class PlanningModule {
public:
    void planRoute(const GPSData &gpsData) {
        std::cout << "Planning route from (" << gpsData.curr_latitude << ", " 
                  << gpsData.curr_longitude << ") to (" << gpsData.dest_latitude 
                  << ", " << gpsData.dest_longitude << ").\n";
    }

    void updateRoute(const CameraData &cameraData, const LidarData &lidarData) {
        if (cameraData.object != CameraData::ObjectType::NONE) {
            std::cout << "Adjusting route for detected object.\n";
        }
        if (lidarData.object != LidarData::ObjectType::ROAD_CURVATURE) {
            std::cout << "Adjusting route for road conditions.\n";
        }
    }
};

// Control Module
class ControlModule {
    PerceptionModule perceptionModule;
    PlanningModule planningModule;

public:
    void initialize(double currLat, double currLon, double destLat, double destLon, GPSData &gpsData) {
        gpsData.curr_latitude = currLat;
        gpsData.curr_longitude = currLon;
        gpsData.dest_latitude = destLat;
        gpsData.dest_longitude = destLon;
    }

    void control(double speed, double direction, GPSData &gpsData, CameraData &cameraData, LidarData &lidarData) {
        perceptionModule.processGPSData(gpsData, speed, direction);
        perceptionModule.processCameraData(cameraData);
        perceptionModule.processLidarData(lidarData);

        planningModule.planRoute(gpsData);
        planningModule.updateRoute(cameraData, lidarData);
    }
};

// Autonomous Driving System
class AutonomousDrivingSystem {
    GPSData gpsData;
    CameraData cameraData;
    LidarData lidarData;
    ControlModule controlModule;

public:
    void initialize(double currLat, double currLon, double destLat, double destLon) {
        controlModule.initialize(currLat, currLon, destLat, destLon, gpsData);
    }

    void runSimulation() {
        double speed = 60.0;  // km/h
        double direction = 45.0;  // degrees
        for (int hour = 0; hour < 24; hour++) {
            std::cout << "Hour " << hour + 1 << ":\n";
            controlModule.control(speed, direction, gpsData, cameraData, lidarData);
            if (sqrt(pow(gpsData.curr_latitude - gpsData.dest_latitude, 2) + pow(gpsData.curr_longitude - gpsData.dest_longitude, 2)) < 25) {
                std::cout << "You have arrived! (close enough).\n";
                break;
            }
        }
    }
};

// Main Function
int main() {
    AutonomousDrivingSystem system;
    double currLat, currLon, destLat, destLon;

    std::cout << "Enter current latitude: ";
    std::cin >> currLat;
    std::cout << "Enter current longitude: ";
    std::cin >> currLon;
    std::cout << "Enter destination latitude: ";
    std::cin >> destLat;
    std::cout << "Enter destination longitude: ";
    std::cin >> destLon;

    system.initialize(currLat, currLon, destLat, destLon);
    system.runSimulation();

    return 0;
}
```

## 6. Questions and Answers

### Q1: Compare Standard and ML Design Patterns
- **Similarity**: Both ensure modularity and reusability.
- **Difference**: ML design patterns focus on managing data and training models, while standard patterns are more general-purpose for software design.

### Q2: Are ML Design Patterns Useful?
Yes, they simplify implementation and scalability, especially for data-intensive applications.

### Q3: Which Standard Pattern Could Be Used?
The **Observer Pattern** is suitable for real-time updates from sensors.

### Q4: Which Other ML Pattern Fits?
The **Pipeline Pattern** is ideal for sequential processing of sensor data.
