# Define executable for algorithms benchmarks
add_executable(optic_algorithms_benchmarks
    # Benchmark source files to be added here
)

# Link the benchmark executable
target_link_libraries(optic_algorithms_benchmarks PRIVATE
    optic_algorithms
    benchmark::benchmark
)
