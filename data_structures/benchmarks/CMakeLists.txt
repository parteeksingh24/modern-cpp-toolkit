# Define executable for data structures benchmarks
add_executable(optic_data_structures_benchmarks
    # Benchmark source files to be added here
)

# Link the benchmark executable
target_link_libraries(optic_data_structures_benchmarks PRIVATE
    optic_data_structures
    benchmark::benchmark
)
