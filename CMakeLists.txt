cmake_minimum_required(VERSION 3.6.0)

project(farmhash)   

    #looking for  files
    file(GLOB SOURCE_FILES "src/farmhash.cc" "farmhash.h")

    file(GLOB HEADER_FILES  "*.h")

    include_directories(${CMAKE_CURRENT_SOURCE_DIR} )

	#lets add specific windows build definitions
	if (WIN32)
		add_compile_definitions(FARMHASH_NO_BUILTIN_EXPECT)
	endif (WIN32)

	#making sure to add the common cpp flags, that are defined in the main cpp file
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${COMMON_CXX_FLAGS}")

	file(COPY ${HEADER_FILES} DESTINATION "${CMAKE_SOURCE_DIR}/SirEngineThe3rdLib/vendors/includes/imgui" )

    #adding the executable
    add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES} ${INCLUDES_FILES})


	if(APPLE)
			MESSAGE(STATUS "Using MACOS")
	elseif(UNIX)
		set(LUA_DEFINITIONS LUA_USE_POSIX)
		target_compile_definitions(${PROJECT_NAME} PUBLIC ${LUA_DEFINITIONS})
	elseif(WIN32)
		#copying built lib to the output
		add_custom_command(
		TARGET ${PROJECT_NAME} 
		POST_BUILD
		COMMAND ${CMAKE_COMMAND} -E copy
		  $<TARGET_FILE:${PROJECT_NAME}>
		  ${CMAKE_SOURCE_DIR}/../builtDependencies/windows/$<CONFIGURATION>/$<TARGET_FILE_NAME:${PROJECT_NAME}>)		
	endif()

	#copying the headers
	file(MAKE_DIRECTORY ${CMAKE_SOURCE_DIR}/../builtDependencies/includes/farmhash)
	file(COPY "src/farmhash.h" DESTINATION ${CMAKE_SOURCE_DIR}/../builtDependencies/includes/farmhash )
