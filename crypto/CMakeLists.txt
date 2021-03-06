set(NTRU_AVX_SRC
  libntrup/src/avx/randomsmall.c
  libntrup/src/avx/weight.c
  libntrup/src/avx/swap.c
  libntrup/src/avx/rq_round3.c
  libntrup/src/avx/rq_recip3.c
  libntrup/src/avx/small.c
  libntrup/src/avx/randomweightw.c
  libntrup/src/avx/dec.c
  libntrup/src/avx/r3_recip.c
  libntrup/src/avx/keypair.c
  libntrup/src/avx/rq_rounded.c
  libntrup/src/avx/mult.c
  libntrup/src/avx/enc.c
  libntrup/src/avx/int32_sort.c
  libntrup/src/avx/rq.c
  libntrup/src/avx/rq_mod3.c
)

set(NTRU_REF_SRC
  libntrup/src/ref/randomsmall.c
  libntrup/src/ref/swap.c
  libntrup/src/ref/rq_round3.c
  libntrup/src/ref/rq_recip3.c
  libntrup/src/ref/small.c
  libntrup/src/ref/rq_mult.c
  libntrup/src/ref/randomweightw.c
  libntrup/src/ref/random32.c
  libntrup/src/ref/dec.c
  libntrup/src/ref/r3_mult.c
  libntrup/src/ref/r3_recip.c
  libntrup/src/ref/keypair.c
  libntrup/src/ref/rq_rounded.c
  libntrup/src/ref/enc.c
  libntrup/src/ref/int32_sort.c
  libntrup/src/ref/rq.c
)

set(NTRU_SRC
  ${NTRU_AVX_SRC}
  ${NTRU_REF_SRC}
  libntrup/src/ntru.cpp
  )

set(CRYPTOGRAPHY_SRC ${NTRU_SRC})

add_library(${CRYPTOGRAPHY_LIB} STATIC ${CRYPTOGRAPHY_SRC})
add_log_tag(${CRYPTOGRAPHY_LIB})

option(DOWNLOAD_SODIUM "Allow libsodium to be downloaded and built locally if not found on the system" OFF)

find_package(Sodium 1.0.17)

if(sodium_FOUND)
  target_include_directories(${CRYPTOGRAPHY_LIB} PUBLIC ${sodium_INCLUDE_DIR})
  target_link_libraries(${CRYPTOGRAPHY_LIB} ${sodium_LIBRARY_RELEASE})
elseif(DOWNLOAD_SODIUM)
  message(STATUS "Sodium >= 1.0.17 not found, but DOWNLOAD_SODIUM specified, so downloading it")
  include(DownloadLibSodium)
  target_link_libraries(${CRYPTOGRAPHY_LIB} sodium_vendor)
else()
  message(FATAL_ERROR "Could not find libsodium >= 1.0.17; either install it on your system or use -DDOWNLOAD_SODIUM=ON to download and build an internal copy")
endif()

target_include_directories(${CRYPTOGRAPHY_LIB} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/libntrup/include")


