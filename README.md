# AR Time Capsule

<p align="center">
  <a href="https://youtu.be/Gk_ktK9_KuM">
    <img src="https://img.shields.io/badge/YouTube-Demo-FF0000?style=for-the-badge&logo=youtube&logoColor=white" alt="YouTube Demo"/>
  </a>
  <a href="https://drive.google.com/file/d/1CiEBe6EB2KbiSJpjvmAJW6P-tWShGiX2/view?usp=sharing">
    <img src="https://img.shields.io/badge/Project-Report-blue?style=for-the-badge&logo=google-drive&logoColor=white" alt="Project Report"/>
  </a>
  <img src="https://img.shields.io/badge/Platform-iOS%20%7C%20Android-brightgreen?style=for-the-badge" alt="Platform"/>
  <img src="https://img.shields.io/badge/Status-Completed-success?style=for-the-badge" alt="Status"/>
</p>

## 📱 Project Overview

AR Time Capsule is a platform where users can record and share memories at specific locations. Users can create time capsules (posts) at specific locations, and other users can view these time capsules through AR when they visit the location. Content can also be shared exclusively within specific groups through the party system.

<details>
<summary>📖 Read more</summary>
<br>
Modern people live reminiscing about the past. In today's society, where it's common to post memorable photos and writings on individual SNS platforms, we've created a new concept of SNS that focuses on vivid experiences and places that hold special memories for people.

AR Time Capsule allows users to vividly experience past memories through AR technology, which is difficult to feel through simple photos or videos. By providing AR content tailored to the user's current location, it enhances immersion and facilitates experience sharing and communication between users.
</details>

## ✨ Key Features

- **🔍 AR Content Experience**: Experience time capsule content through AR at the user's current location
- **📍 Location-Based Service**: Provides time capsule content within 1km of the user's location
- **👥 Party System**: Users can form groups to share content
- **💬 SNS Features**: Comment and like functions for posts
- **👤 User Management**: Profile management and personal post management

## 🎬 Demo Video

<p align="center">
  <a href="https://youtu.be/Gk_ktK9_KuM">
    <img src="https://img.youtube.com/vi/Gk_ktK9_KuM/0.jpg" alt="AR Time Capsule Demo Video" width="600"/>
  </a>
</p>

<p align="center">
  <i>👆 Click the image to watch the demo video on YouTube</i>
</p>

## 🛠️ Tech Stack

### Frontend
![Flutter](https://img.shields.io/badge/Flutter-02569B?style=for-the-badge&logo=flutter&logoColor=white)
![Unity](https://img.shields.io/badge/Unity-000000?style=for-the-badge&logo=unity&logoColor=white)
![Dart](https://img.shields.io/badge/Dart-0175C2?style=for-the-badge&logo=dart&logoColor=white)
![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=c-sharp&logoColor=white)

### Backend
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)
![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=java&logoColor=white)

### Database
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)

### API & Cloud
![Google Maps](https://img.shields.io/badge/Google_Maps-4285F4?style=for-the-badge&logo=google-maps&logoColor=white)
![GCP](https://img.shields.io/badge/Google_Cloud-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)

## 💻 Core Logic & Implementation

Since the full source code is private due to team collaboration policies, here are snippets of the core logic demonstrating key technical capabilities.

<details>
<summary><b>1. Real-time Location & Map Management (Frontend/Flutter)</b></summary>
<br>

* **Description**: Manages real-time user location updates and renders custom markers for Time Capsules on Google Maps.
* **Key Logic**:
    * Uses `Geolocator` stream to track position changes.
    * Dynamically updates camera position and user markers.
    * Renders custom `BitmapDescriptor` markers for optimization.

```dart
class LocationController extends GetxController {
  Rx<Position?> currentPosition = Rx<Position?>(null);
  RxSet<Marker> capsuleMarkers = RxSet();
  GoogleMapController? mapController;

  final LocationSettings locationSettings = const LocationSettings(
    accuracy: LocationAccuracy.bestForNavigation,
    distanceFilter: 5, // Update every 5 meters
  );

  // Real-time location tracking
  getLocationUpdates() async {
    positionStreamSubscription =
        Geolocator.getPositionStream(locationSettings: locationSettings)
            .listen((Position position) {
      currentPosition.value = position;
      updateCameraPosition(position);
      addUserMarker('images/profile.png', position);
    });
  }

  // Update map camera view smoothly
  void updateCameraPosition(Position position) {
    mapController?.animateCamera(CameraUpdate.newCameraPosition(CameraPosition(
      target: LatLng(position.latitude, position.longitude),
      zoom: currentZoom,
    )));
  }

  // Generate Custom Capsule Markers
  Future<void> addCapsuleMarker(String address, CapsuleModel capsule) async {
    final Marker capsuleMarker = Marker(
      position: LatLng(capsule.latitude, capsule.longitude),
      markerId: MarkerId(capsule.cid.toString()),
      icon: await customMarker.getMarkerIcon(
          imagePath: address,
          size: const Size(125.0, 125.0),
          mainColor: capsuleColor,
          borderColor: userBorderColor),
      onTap: () => capsuleDetail.capsuleOntap(capsule),
    );
    capsuleMarkers.add(capsuleMarker);
  }
}

```

</details>

<details>
<summary><b>2. Capsule Data Transaction (Frontend/Flutter)</b></summary>




* **Description**: Handles the creation and transmission of new Time Capsules to the backend server.
* **Key Logic**: Serializes the `CapsuleModel` to JSON and handles HTTP responses via GetX controller.

```dart
class CapsuleController extends GetxController {
  // Send new capsule data to server
  static Future<CapsuleModel?> sendCapsule(
      String endpoint, CapsuleModel capsule) async {
    try {
      var response = await http.post(Uri.parse(endpoint),
          headers: <String, String>{
            'Content-Type': 'application/json; charset=UTF-8',
          },
          body: jsonEncode(capsule.toJson()));

      if (response.statusCode == 200) {
        return capsule;
      } else {
        throw Exception('Failed to send capsule');
      }
    } catch (e) {
      print("Error sending capsule: $e");
      return null;
    }
  }
}

```

</details>

<details>
<summary><b>3. Interactive UI Animation (Frontend/Flutter)</b></summary>




* **Description**: Custom animation controller for the Floating Action Button (FAB) menu using `AnimationController` and `CurvedAnimation`.

```dart
class FabController extends GetxController with GetSingleTickerProviderStateMixin {
  final RxBool open = false.obs;
  late AnimationController _controller;
  late Animation<double> expandAnimation;

  @override
  void onInit() {
    super.onInit();
    _controller = AnimationController(
        value: open.value ? 0.0 : 1.0, 
        duration: Duration(milliseconds: 275), 
        vsync: this
    );
    expandAnimation = CurvedAnimation(
        parent: _controller, 
        curve: Curves.fastOutSlowIn
    );
  }

  void toggle() {
    open.value = !open.value;
    if (open.value) {
      _controller.forward();
    } else {
      _controller.reverse();
    }
  }
}

```

</details>

<details>
<summary><b>4. Data Model Structure (Frontend/Dart)</b></summary>




* **Description**: Defines the core data structure for Time Capsules, including geographical coordinates and content metadata.
* **Key Logic**:
* Implements strict typing for API data integrity.
* Handles JSON serialization/deserialization, mapping nested JSON objects (location data) to flat model properties.



```dart
class CapsuleModel {
  final int? cid;
  final String title;
  final String contents;
  final double latitude;
  final double longitude;
  final String locationName;
  final DateTime createdAt;
  final String? image; 
  final int like;
  final String nickname;
  final List<dynamic> capsuleComment;

  CapsuleModel({
    this.cid,
    required this.title,
    required this.contents,
    required this.latitude,
    required this.longitude,
    required this.locationName,
    required this.createdAt,
    this.image,
    required this.like,
    required this.nickname,
    required this.capsuleComment,
  });

  // Serialization for API requests
  Map<String, dynamic> toJson() => {
        'cid': cid,
        'title': title,
        'contents': contents,
        'latitude': latitude,
        'longitude': longitude,
        'locationName': locationName,
        'createdAt': createdAt.toIso8601String(),
        'image': image,
        'like': like,
        'nickname': nickname,
        'capsuleComment': capsuleComment,
      };

  // Deserialization from Server Response
  CapsuleModel.fromJson(Map<String, dynamic> json)
      : cid = json['cid'],
        title = json['title'],
        contents = json['contents'],
        // Parsing nested location data from server
        latitude = json['location']['latitude'].toDouble(),
        longitude = json['location']['longitude'].toDouble(),
        locationName = json['locationName'],
        createdAt = DateTime.parse(json['createdAt']),
        image = json['image'],
        like = json['like'],
        nickname = json['nickname'],
        capsuleComment = List<dynamic>.from(json['capsuleComment']);
}

```

</details>

<details>
<summary><b>5. API Communication Service (Frontend/Dart)</b></summary>




* **Description**: Centralized service layer for handling HTTP requests, including fetching capsule lists and integrating with Unity.
* **Key Logic**:
* **`fetchCapsuleList`**: Retrieves and maps JSON data to `CapsuleModel` objects asynchronously.
* **`prepareImageForUnity`**: Encodes image assets to Base64 strings to pass texture data to the Unity engine.



```dart
class ApiService {
  // Fetch list of capsules from the server
  static Future<List<CapsuleModel>?> fetchCapsuleList(String endpoint) async {
    try {
      var response = await http.get(Uri.parse(endpoint));
      
      if (response.statusCode == 200) {
        final List<dynamic> parsed = json.decode(response.body);
        return parsed.map<CapsuleModel>((json) => CapsuleModel.fromJson(json)).toList();
      } else {
        throw Exception('Failed to load capsule data');
      }
    } catch (e) {
      print("Error fetching capsules: $e");
      return null;
    }
  }

  // Utilities for Unity Integration
  static Future<String> prepareImageForUnity(String path) async {
    ByteData imageData = await rootBundle.load(path);
    List<int> bytes = imageData.buffer.asUint8List();
    return base64Encode(bytes); // Pass this string to Unity
  }
}

```

</details>

<details>
<summary><b>6. UI/UX & Map Integration (Frontend/Flutter)</b></summary>




* **Description**: Showcasing sophisticated UI handling using Google Maps and advanced animation libraries.
* **Key Features**:
* **Interactive Map**: Renders user location and capsule markers dynamically using `Obx` (GetX state management).
* **Immersive Home**: Implemented `SliverAppBar` for expanding headers and `flutter_staggered_animations` for smooth list transitions.



**A. Map Page (Google Maps & AR Entry)**

```dart
class MapPage extends StatelessWidget {
  final LocationController locationController = Get.find<LocationController>();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      extendBodyBehindAppBar: true, // Transparent AppBar effect
      body: Obx(() {
        return GoogleMap(
          markers: Set<Marker>.from(locationController.userMarkers.value
              .union(locationController.capsuleMarkers.value)),
          circles: {
            Circle(
              circleId: const CircleId("userRadius"),
              center: LatLng(
                  locationController.currentPosition.value!.latitude,
                  locationController.currentPosition.value!.longitude),
              radius: 100, // Interaction radius
              fillColor: Colors.blueAccent.withOpacity(0.1),
              strokeColor: Colors.blueAccent,
              strokeWidth: 2,
            )
          },
          myLocationEnabled: true,
          initialCameraPosition: CameraPosition(
            target: LatLng(
              locationController.currentPosition.value!.latitude,
              locationController.currentPosition.value!.longitude,
            ),
            zoom: 16.0,
          ),
          onMapCreated: locationController.onMapCreated,
        );
      }),
      // Button to switch to AR View
      floatingActionButton: FloatingActionButton(
        onPressed: () => Get.to(() => const UnityAr()),
        child: Icon(CupertinoIcons.camera_on_rectangle_fill),
      ),
    );
  }
}

```

**B. Home Screen (Sliver & Animations)**

```dart
class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: CustomScrollView(
        slivers: <Widget>[
          // Collapsing AppBar with Gradient Background
          SliverAppBar(
            expandedHeight: MediaQuery.of(context).size.height * 0.25,
            pinned: true,
            flexibleSpace: FlexibleSpaceBar(
              title: Text('CapInNet', style: GoogleFonts.poppins(fontWeight: FontWeight.bold)),
              background: Stack(
                fit: StackFit.expand,
                children: [
                  Image.asset('assets/background.jpg', fit: BoxFit.cover),
                  Container(
                    decoration: BoxDecoration(
                      gradient: LinearGradient(
                        colors: [Colors.transparent, Colors.black.withOpacity(0.7)],
                        begin: Alignment.topCenter,
                        end: Alignment.bottomCenter,
                      ),
                    ),
                  ),
                ],
              ),
            ),
          ),
          // Staggered List Animation
          SliverToBoxAdapter(
            child: AnimationLimiter(
              child: ListView.builder(
                shrinkWrap: true,
                physics: NeverScrollableScrollPhysics(),
                itemCount: 3,
                itemBuilder: (BuildContext context, int index) {
                  return AnimationConfiguration.staggeredList(
                    position: index,
                    duration: const Duration(milliseconds: 375),
                    child: SlideAnimation(
                      verticalOffset: 50.0,
                      child: FadeInAnimation(child: _buildPostItem(context)),
                    ),
                  );
                },
              ),
            ),
          ),
        ],
      ),
    );
  }
}

```

</details>

<details>
<summary><b>7. AR Engine Integration (Flutter ↔ Unity Bridge)</b></summary>




* **Description**: Establishes a bidirectional communication bridge between the Flutter UI layer and the Unity AR engine.
* **Key Logic**:
* **Flutter to Unity**: Encodes capsule images to **Base64** strings and transmits them via `postMessage`.
* **Unity to Flutter**: Uses `ARRaycastManager` to detect touches on 3D objects and sends the object ID back to Flutter to open the detail screen.



**A. Flutter Side: Sending Data to Unity**

```dart
class _UnityArState extends State<UnityAr> {
  late UnityWidgetController _unityWidgetController;

  void onUnityCreated(UnityWidgetController controller) {
    _unityWidgetController = controller;
    sendCapsuleDataToUnity();
  }

  // Converts image to Base64 and sends to Unity
  Future<void> sendCapsuleDataToUnity() async {
    for (var capsule in capsuleController.nearCapsuleList) {
      if (capsule?.image != null) {
        File imageFile = File(capsule!.image!); 
        List<int> imageBytes = await imageFile.readAsBytes();
        String base64Image = base64Encode(imageBytes);

        // Protocol: "ID,Title,Base64String"
        String message = "${capsule.cid},${capsule.title},$base64Image";
        
        _unityWidgetController.postMessage(
            'CapsuleSpawner', 'receiveMessage', message);
      }
    }
  }
}

```

**B. Unity Side: Receiving Data & Handling Interaction (C#)**

```csharp
public class ArPlaceOnPlane : MonoBehaviour
{
    public GameObject placeObject; // Capsule Prefab
    public List<GameObject> spawnedCapsules = new List<GameObject>();

    // Called from Flutter
    public void receiveMessage(string message)
    {
        string[] parts = message.Split(',');
        int cid = int.Parse(parts[0]);
        string title = parts[1];
        // string imageBase64 = parts[2]; (Processed elsewhere)

        capsuleCreate(cid, title);
    }

    public void capsuleCreate(int cid, string title)
    {
        // Calculate spawn position relative to camera
        Vector3 cameraPosition = Camera.main.transform.position;
        Vector3 cameraForward = Camera.main.transform.forward;
        float distance = Random.Range(4.0f, 8.0f);
        Vector3 spawnPosition = cameraPosition + cameraForward * distance;

        GameObject capsule = Instantiate(placeObject, spawnPosition, Quaternion.identity);
        
        // Assign Data to Capsule Script
        capsule.GetComponent<CapsuleDetail>().assignCid(cid);
        capsule.GetComponent<CapsuleDetail>().assignTitle(title);
        
        spawnedCapsules.Add(capsule);
    }
}

```

**C. Unity Side: Touch Detection & Sending Back to Flutter (C#)**

```csharp
public class CapsuleTouchHandler : MonoBehaviour
{
    private CapsuleDetail capsuleDetail;

    void Update()
    {
        if (Input.touchCount > 0 && Input.GetTouch(0).phase == TouchPhase.Began)
        {
            HandleTouch(Input.GetTouch(0).position);
        }
    }

    private void HandleTouch(Vector2 touchPosition)
    {
        Ray ray = Camera.main.ScreenPointToRay(touchPosition);
        RaycastHit hit;

        if (Physics.Raycast(ray, out hit, Mathf.Infinity))
        {
            // Check if the touched object is our Capsule
            if (hit.collider.gameObject.name == "polySurface14") 
            {
                int cid = capsuleDetail.getCid();
                string title = capsuleDetail.getTitle();
                
                // Send message back to Flutter to open Detail Screen
                sendToFlutter($"touchImage,{cid},{title}");
                ShowImageOnCapsule(hit.point); // Visual Feedback
            }
        }
    }

    public void sendToFlutter(string message)
    {
        UnityMessageManager.Instance.SendMessageToFlutter(message);
    }
}

```

</details>

## 📱 Screenshots

<p align="center">
<img src="./assets/images/screenshot1.png" width="200" />
<img src="./assets/images/screenshot2.png" width="200" />
<img src="./assets/images/screenshot3.png" width="200" />
</p>

### Screenshot Descriptions

1. **AR Camera View**: This screenshot shows the actual AR camera view where capsule-shaped AR objects dynamically move in the environment. Each capsule displays its title above it. Users can only see these capsules when they visit the actual location where the capsule was saved. By clicking on a capsule, users can view the photos and content inside.
2. **Map View**: The map page allows users to see the real-time locations of their time capsules. The color coding of the capsules indicates whether they were posted by the user themselves or by someone they follow, making it easy to distinguish between different types of content.
3. **Global Persistence**: This screenshot demonstrates that once a time capsule is saved, it remains accessible regardless of distance. Even if there are differences in distance between countries, the capsules are maintained in their original locations, showcasing the global nature of the application.

## 💡 Implementation Technologies

### Flutter

* Cross-platform UI development
* Smooth user experience using animation framework
* State management and responsive design implementation
* Efficient data processing through asynchronous programming

### Unity

* Augmented reality implementation using AR Foundation
* Realistic AR experience through physics-based algorithms
* Vivid content expression using animation system
* Efficient interaction management through event system

### Spring Boot

* RESTful API design and implementation
* Distance calculation algorithm using latitude/longitude
* Database integration and optimization
* Security and authentication system implementation

## 🚀 Challenges and Solutions

<details>
<summary>AR Content Rendering Optimization</summary>





We conducted continuous testing and improvement to maintain a balance between AR content quality and performance, considering various device environments. In particular, we applied rendering optimization techniques to provide a smooth experience even on low-spec devices.
</details>

<details>
<summary>Natural Placement of AR Content</summary>





We implemented natural placement of AR content through user pose estimation, surrounding environment recognition, real-time feedback, and correction. This allows users to experience AR that blends naturally with the real world.
</details>

<details>
<summary>Location-Based Service Accuracy</summary>





We provided accurate location information using Google Maps API and device GPS, and optimized the algorithm for searching time capsules within 1km. This allows users to accurately find time capsules around their location.
</details>

<details>
<summary>User Experience Improvement</summary>





We continuously collected and analyzed user feedback to improve UI/UX and design intuitive interfaces. In particular, we implemented a user-friendly interface so that even users who are new to AR can easily use it.
</details>

## 📊 Project Progress

| Feature | Status |
| --- | --- |
| Sign up/Login | ✅ 100% |
| Party Management | ✅ 100% |
| Capsule (Post) Creation | ✅ 100% |
| User Management | ✅ 100% |
| Capsule Content Viewing | ✅ 100% |
| Location-Based Content Provision | ✅ 100% |
| SNS Features | ✅ 100% |
| Notifications | ✅ 100% |

## 💫 Expected Effects

* Providing users with an immersive AR experience
* Enhancing realism through AR content that integrates naturally with the real environment
* Creating a new social experience through the combination of location-based services and AR technology
* Presenting a new paradigm for sharing memories

## 📚 Related Materials

* [Project Report (PDF)](https://drive.google.com/file/d/1CiEBe6EB2KbiSJpjvmAJW6P-tWShGiX2/view?usp=sharing)

## 🔗 References

* [Spring Boot Distance Calculation Algorithm Using Latitude and Longitude](https://kyu-nahc.tistory.com/entry/Spring-boot-%EC%9C%84%EB%8F%84-%EA%B2%BD%EB%8F%84%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EB%8A%94-%EA%B1%B0%EB%A6%AC-%EA%B3%84%EC%82%B0-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)
* [GCP Server Deployment](https://velog.io/@k_k_s__h/Spring-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-GCP-Google-Cloud-Platform-%EC%84%9C%EB%B2%84-%EB%B0%B0%ED%8F%AC)
* [Creating Intuitive UI](https://fastercapital.com/ko/content/%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4--%EA%B0%84%EB%8B%A8%ED%95%98%EA%B3%A0-%EC%A7%81%EA%B4%80%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EB%94%94%EC%9E%90%EC%9D%B8%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95.html)
* [Creating AR Games with Unity](https://velog.io/@topnotchsh/%EC%9C%A0%EB%8B%88%ED%8B%B0%EB%A1%9C-AR-%EA%B2%8C%EC%9E%84-%EB%A7%8C%EB%93%A4%EA%B8%B0)

---

<p align="center">
<i>2024 Hanium ICT Competition hosted by Korea Government</i>
</p>

