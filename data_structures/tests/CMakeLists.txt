# Add executable for data structure tests
add_executable(optic_data_structures_tests
    # Test source files to be added here
)

# Link the test executable with Google Test and our library components
target_link_libraries(optic_data_structures_tests PRIVATE
    optic_data_structures
    gtest
    gtest_main
)

# Discover tests automatically
include(GoogleTest)
gtest_discover_tests(optic_data_structures_tests)
