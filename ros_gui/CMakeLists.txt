##############################################################################
# CMake
##############################################################################

cmake_minimum_required(VERSION 2.8.0)
project(ros_gui)

add_definitions(-std=c++11)
add_compile_options(-std=c++11)
##############################################################################
# Catkin
##############################################################################
list(APPEND CMAKE_PREFIX_PATH "/opt/ros/kinetic")
#list(APPEND CMAKE_PREFIX_PATH "../../devel")
set(custom_msgs_DIR /home/mty/catkin_gui/devel/share/custom_msgs/cmake)
# qt_build provides the qt cmake glue, roscpp the comms for a default talker
find_package(catkin REQUIRED COMPONENTS custom_msgs qt_build roscpp rospy std_msgs sensor_msgs rviz cv_bridge image_transport move_base_msgs tf)
find_package(Qt5 REQUIRED COMPONENTS Core Gui Widgets Concurrent)
find_package(OpenCV REQUIRED)

include_directories(${catkin_INCLUDE_DIRS})
include_directories(${Qt5_INCLUDE_DIRS})
include_directories(${OpenCV_INCLUDE_DIRS})
INCLUDE_DIRECTORIES("include/ros_gui")
# Use this to define what the package will export (e.g. libs, headers).
# Since the default here is to produce only a binary, we don't worry about
# exporting anything. 



catkin_package(
)

##############################################################################
# Qt Environment
##############################################################################

# this comes from qt_build's qt-ros.cmake which is automatically 
# included via the dependency call in package.xml
#rosbuild_prepare_qt4(QtCore QtGui) # Add the appropriate components to the component list here

##############################################################################
# Sections
##############################################################################


file(GLOB QT_FORMS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ui/*.ui)
file(GLOB QT_RESOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} resources/*.qrc)
file(GLOB_RECURSE QT_MOC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS include/ros_gui/*.h ../build-ros_gui-Desktop-Default/*.h)

QT5_ADD_RESOURCES(QT_RESOURCES_CPP ${QT_RESOURCES})
QT5_WRAP_UI(QT_FORMS_HPP ${QT_FORMS})
QT5_WRAP_CPP(QT_MOC_HPP ${QT_MOC})

##############################################################################
# Sources
##############################################################################

file(GLOB_RECURSE QT_SOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS src/*.cpp)

##############################################################################
# Binaries
##############################################################################

add_executable(ros_gui ${QT_SOURCES} ${QT_RESOURCES_CPP} ${QT_FORMS_HPP} ${QT_MOC_HPP})
target_link_libraries(ros_gui  ${catkin_LIBRARIES} Qt5::Core Qt5::Gui Qt5::Widgets ${OpenCV_LIBRARIES})
install(TARGETS ros_gui
	RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
