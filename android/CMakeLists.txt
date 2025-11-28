cmake_minimum_required(VERSION 3.4.1)

file(GLOB_RECURSE cppPaths "../ios/Stockfish/src/*.cpp")
add_library(
  stockfish
  SHARED
  ../ios/FlutterStockfish/ffi.cpp
  ${cppPaths}
)

# ✅ Siempre enlazar con soporte de página de 16 KB en Android
if(ANDROID)
  target_link_options(stockfish PRIVATE -Wl,-z,max-page-size=16384)
endif()

# ✅ Flags de compilación
if(ANDROID_ABI STREQUAL arm64-v8a)
  # 64 bits con NEON y POPCNT
  target_compile_options(stockfish PRIVATE
    -fno-exceptions
    -DUSE_PTHREADS
    -DNDEBUG
    -O3
    -DIS_64BIT
    -DUSE_POPCNT
    -DUSE_NEON=8
  )
else()
  # Otras ABIs (x86_64, etc.)
  target_compile_options(stockfish PRIVATE
    -fno-exceptions
    -DUSE_PTHREADS
    -DNDEBUG
    -O3
  )
endif()

# Los NNUE que ya tenías
file(DOWNLOAD https://tests.stockfishchess.org/api/nn/nn-1111cefa1111.nnue
  ${CMAKE_BINARY_DIR}/nn-1111cefa1111.nnue)

file(DOWNLOAD https://tests.stockfishchess.org/api/nn/nn-37f18f62d772.nnue
  ${CMAKE_BINARY_DIR}/nn-37f18f62d772.nnue)