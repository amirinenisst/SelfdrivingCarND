
# Extended-Kalman-Filter

The main objective of this project is to utilize the sensor data from both LIDAR and RADAR measurements and track the objects like pedestrian, vehicles or any other moving objects with the help of Extended kalman filter.

## Building
The main program can be built and run by doing the following from the project top directory.
mkdir build
cd build
cmake ..
make
./ExtendedKF
	
## Code Flow


When the connections are established the simulator starts and then the Code begins from main.cpp. The process flow is like the main.cpp calls FusionEFK.cpp to make initialization, update and predict which are given in kalman_filter.cpp. RMSE and Jacobian matrix are given in tools.cpp.


## Results: <a name="results"></a>

- Dataset 1
![](images/img.JPG) 

- Dataset 2
![](images/img1.JPG) 

