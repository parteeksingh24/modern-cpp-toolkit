# Collect all benchmark source files
file(GLOB BENCHMARK_SOURCES "*.cpp")

# Only create the target if there are source files
if(BENCHMARK_SOURCES)
    add_executable(optic_data_structures_benchmarks ${BENCHMARK_SOURCES})
    target_link_libraries(optic_data_structures_benchmarks PRIVATE
        optic_data_structures
        benchmark::benchmark
    )
else()
    message(STATUS "No benchmark sources found for optic_data_structures_benchmarks")
endif()
