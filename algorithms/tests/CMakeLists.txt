# Add executable for algorithm tests
add_executable(optic_algorithms_tests
    # Test source files to be added here
)

# Link the test executable with Google Test and our library components
target_link_libraries(optic_algorithms_tests PRIVATE
    optic_algorithms
    gtest
    gtest_main
)

# Discover tests automatically
include(GoogleTest)
gtest_discover_tests(optic_algorithms_tests)
