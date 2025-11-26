cmake_minimum_required(VERSION 3.16)
project(eigen_checks)

if(NOT CMAKE_CXX_STANDARD)
	set(CMAKE_CXX_STANDARD 14)
endif()
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_compile_options(
		-Wall
		-Wextra
		-pedantic
)

# Build system
find_package(ament_cmake REQUIRED)

# Eigen is best pulled in via eigen3_cmake_module in ROS 2
find_package(eigen3_cmake_module REQUIRED)
find_package(Eigen3 REQUIRED)

# Glog / gflags assumed to come from system or vendor packages
# (rosdep keys: 'glog', 'gflags')
find_package(glog REQUIRED)
find_package(gflags REQUIRED)

find_package(Threads REQUIRED)

# Library
add_library(${PROJECT_NAME} src/eigen-checks.cc)

target_include_directories(${PROJECT_NAME}
		PUBLIC
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
		$<INSTALL_INTERFACE:include>
)

target_link_libraries(${PROJECT_NAME}
		PUBLIC
		Eigen3::Eigen
		glog::glog
		gflags
		Threads::Threads
)

# Export targets & dependencies for find_package(eigen_checks)
ament_export_targets(export_${PROJECT_NAME} HAS_LIBRARY_TARGET)
ament_export_dependencies(
		eigen3_cmake_module
		Eigen3
		glog
		gflags
)
ament_export_include_directories(include)

# Tests (rough port of catkin_add_gtest → ament_add_gtest)
if(BUILD_TESTING)
	find_package(ament_cmake_gtest REQUIRED)

	set(TEST_LIBS ${PROJECT_NAME} Threads::Threads)

	ament_add_gtest(test_gtest_near
			test/test_gtest-near.cc)
	if(TARGET test_gtest_near)
		target_link_libraries(test_gtest_near ${TEST_LIBS})

	endif()

	ament_add_gtest(test_gtest_equal
			test/test_gtest-equal.cc)
	if(TARGET test_gtest_equal)
		target_link_libraries(test_gtest_equal ${TEST_LIBS})
	endif()

	ament_add_gtest(test_gtest_zero
			test/test_gtest-zero.cc)
	if(TARGET test_gtest_zero)
		target_link_libraries(test_gtest_zero ${TEST_LIBS})
	endif()

	ament_add_gtest(test_gtest_equal_double
			test/test_gtest-equal-double.cc)
	if(TARGET test_gtest_equal_double)
		target_link_libraries(test_gtest_equal_double ${TEST_LIBS})
	endif()

	ament_add_gtest(test_glog_near
			test/test_glog-near.cc)
	if(TARGET test_glog_near)
		target_link_libraries(test_glog_near ${TEST_LIBS})
	endif()

	ament_add_gtest(test_glog_equal
			test/test_glog-equal.cc)
	if(TARGET test_glog_equal)
		target_link_libraries(test_glog_equal ${TEST_LIBS})
	endif()

	ament_add_gtest(test_glog_zero
			test/test_glog-zero.cc)
	if(TARGET test_glog_zero)
		target_link_libraries(test_glog_zero ${TEST_LIBS})
	endif()

	ament_add_gtest(test_glog_equal_double
			test/test_glog-equal-double.cc)
	if(TARGET test_glog_equal_double)
		target_link_libraries(test_glog_equal_double ${TEST_LIBS})
	endif()

	ament_add_gtest(test_glog_zero_sized_types
			test/test_glog-zero-sized-types.cc)
	if(TARGET test_glog_zero_sized_types)
		target_link_libraries(test_glog_zero_sized_types ${TEST_LIBS})
	endif()

	ament_add_gtest(test_gtest_zero_sized_types
			test/test_gtest-zero-sized-types.cc)
	if(TARGET test_gtest_zero_sized_types)
		target_link_libraries(test_gtest_zero_sized_types ${TEST_LIBS})
	endif()
endif()

# Install
install(
		TARGETS ${PROJECT_NAME}
		EXPORT export_${PROJECT_NAME}
		ARCHIVE DESTINATION lib
		LIBRARY DESTINATION lib
		RUNTIME DESTINATION bin
)

install(
		DIRECTORY include/
		DESTINATION include
)

ament_package()
