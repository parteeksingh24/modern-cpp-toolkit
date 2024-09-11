# Collect all test source files
file(GLOB TEST_SOURCES "*.cpp")

# Only create the target if there are source files
if(TEST_SOURCES)
    add_executable(optic_data_structures_tests ${TEST_SOURCES})
    target_link_libraries(optic_data_structures_tests PRIVATE
        optic_data_structures
        gtest
        gtest_main
    )
    gtest_discover_tests(optic_data_structures_tests)
else()
    message(STATUS "No test sources found for optic_data_structures_tests")
endif()
