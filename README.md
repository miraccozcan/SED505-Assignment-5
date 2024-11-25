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
![UML Diagram](https://github.com/miraccozcan/SED505-Assignment-5/blob/main/UML%20CAMERA.png)

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
- **Similarity**: 
  - Both ensure modularity, reusability, and scalability, enabling systems to be extended or modified with minimal impact on other components.
- Both encourage separation of concerns, ensuring that each component handles a specific responsibility, which simplifies debugging and collaboration in development teams.
Example: In the autonomous driving system, both standard and ML design patterns focus on breaking down tasks into modules like PerceptionModule, PlanningModule, and ControlModule.

- **Difference**: 
- Focus:
Standard design patterns (e.g., Singleton, Observer, Factory) are general-purpose and focus on structuring code, managing object creation, and ensuring efficient communication between components.
ML design patterns emphasize managing data pipelines, handling training workflows, and optimizing model performance.
- Data-Centric vs. Process-Centric:
ML design patterns are data-centric—they revolve around handling large datasets, pre-processing, and training models.
Standard design patterns are process-centric—they deal with program structure and execution flow.
- Example:
Standard: A Factory Pattern might be used to instantiate different sensors (Camera, LIDAR).
ML: The Data Augmentation Pattern is used to generate more training data for object detection.

### Q2: Are ML Design Patterns Useful?
Yes, ML design patterns are highly useful for the following reasons:
- Simplify Complex ML Workflows:
They provide structured methods to handle common ML challenges, such as data preparation, model evaluation, and deployment.
Example: Using the Transfer Learning Pattern allows a model trained on one task (e.g., object detection) to be adapted to a related task (e.g., pedestrian detection).
- Enhance Scalability:
ML design patterns enable smooth scaling from small datasets to large distributed systems, making them ideal for production-grade machine learning.
Example: The Pipeline Pattern automates data preprocessing, model training, and evaluation steps.
- Promote Reusability:
By abstracting ML tasks into reusable components, ML patterns reduce the need to rewrite workflows for every project.
- Improve Collaboration:
Patterns like the Feature Store Pattern standardize the way features are shared and reused across teams in large ML projects.


### Q3: Which Standard Pattern Could Be Used?
**Observer Pattern:**
The Observer Pattern is highly suitable for real-time systems like autonomous driving, where sensors constantly update the system with new data.
**How It Works:**
The PerceptionModule acts as the "subject," broadcasting updates to the PlanningModule and ControlModule.
Changes in one module (e.g., detecting an obstacle) immediately notify dependent modules, triggering necessary updates.
Example:
If a CameraData object detects a pedestrian, the PerceptionModule notifies the PlanningModule to adjust the route and the ControlModule to slow down.

**Strategy Pattern:**
The Strategy Pattern can be used to dynamically switch behaviors based on environmental conditions.
**How It Works:**
The ControlModule chooses different driving strategies (e.g., highway mode vs. urban mode) based on GPS location and sensor inputs.
Example:
On highways, the system uses a "high-speed" strategy, while in cities, it switches to a "cautious" strategy.

### Q4: Which Other ML Pattern Fits?
**1. Pipeline Pattern**
The Pipeline Pattern is ideal for sequential data processing tasks in machine learning.
**How It Works:**
Sensor data flows through a pipeline for preprocessing (noise filtering), feature extraction, and decision-making.
**Example:**
In the autonomous driving system:
Camera data is preprocessed to identify objects.
LIDAR data is used to detect obstacles.
GPS data is used for localization.
These are combined sequentially to update the vehicle's route and actions.

**2. Data Augmentation Pattern**
This pattern is useful for enhancing the training dataset by generating synthetic variations.
**How It Works:**
Applies transformations like rotation, scaling, or flipping to existing image datasets.
**Example:**
Training an object detection model for the CameraData may involve augmenting pedestrian images with different lighting conditions or angles to improve robustness.


**3. Ensemble Learning Pattern**
Combines multiple models to improve prediction accuracy.
**How It Works:**
Uses a collection of models (e.g., a combination of LIDAR and Camera models) to make decisions.
**Example:**
In the autonomous system, an ensemble of object detection models can combine LIDAR and Camera data to reduce false positives.


**4. Transfer Learning Pattern**
This pattern leverages a pre-trained model and fine-tunes it for a specific task.
**How It Works:**
Use a model trained on a large dataset (e.g., ImageNet) and adapt it for detecting traffic signs in CameraData.
**Example:**
A pre-trained object detection model could be fine-tuned to specifically identify vehicles, pedestrians, and bicycles.


**5. Feature Store Pattern**
Centralizes feature engineering for reusability across teams or projects.
**How It Works:**
Features extracted from CameraData, LidarData, and GPSData are stored in a centralized repository for use by multiple modules.
**Example:**
Precomputed GPS waypoints could be reused across different routes in the system.
