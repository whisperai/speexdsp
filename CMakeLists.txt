cmake_minimum_required(VERSION 3.4)
project(speexdsp)

include(CheckTypeSize)
check_type_size("int16_t" SIZEOF_INT16)

set(SPEEXDSP_FOLDER $ENV{WHISPER_SYSTEM_ROOT}/libraries/third_party/speexdsp)
set(SPEEXDSP_SOURCE ${SPEEXDSP_FOLDER}/libspeexdsp)
set(SPEEXDSP_INCLUDE ${SPEEXDSP_FOLDER}/include)

set(SIZE16 "int16_t")
set(USIZE16 "uint16_t")
set(SIZE32 "int32_t")
set(USIZE32 "uint32_t")

if(NOT (WIN32 OR APPLE OR CYGWIN OR HAIKU OR BEOS))
    set(FIXED_POINT 1)
    set(FLOATING_POINT 0)
    set(USE_SSE 0)
    set(USE_NEON 1)
    set(USE_SSE2 0)
    set(DISABLE_FLOAT_API 0)
    set(ARM4_ASM 0)
    set(ARM5E_ASM 0)
else()
    set(FIXED_POINT 0)
    set(FLOATING_POINT 1)
    set(USE_SSE 0)
    set(USE_NEON 0)
    set(USE_SSE2 1)
    set(DISABLE_FLOAT_API 0)
    set(ARM4_ASM 0)
    set(ARM5E_ASM 0)
endif()

set(HAVE_STDINT_H 1)
set(VAR_ARRAYS 1)
set(USE_ALLOCA 1)
set(EXPORT "__attribute__((visibility(\"default\")))")
set(ENABLE_VALGRIND 0)
set(RESAMPLE_FULL_SINC_TABLE 1)
set(USE_KISS_FFT 1)
set(USE_SMALLFT 0)
set(USE_GPL_FFTW3 0)
set(USE_INTEL_MKL 0)

configure_file(config.h.cmake ${SPEEXDSP_FOLDER}/libspeexdsp/config.h)

add_library(speexdsp STATIC
    ${SPEEXDSP_SOURCE}/buffer.c
    ${SPEEXDSP_SOURCE}/fftwrap.c
    ${SPEEXDSP_SOURCE}/filterbank.c
    ${SPEEXDSP_SOURCE}/jitter.c
    ${SPEEXDSP_SOURCE}/kiss_fft.c
    ${SPEEXDSP_SOURCE}/kiss_fftr.c
    ${SPEEXDSP_SOURCE}/mdf.c
    ${SPEEXDSP_SOURCE}/preprocess.c
    ${SPEEXDSP_SOURCE}/scal.c
    ${SPEEXDSP_SOURCE}/resample.c
)

target_compile_options(speexdsp PRIVATE "-DHAVE_CONFIG_H")
target_include_directories(speexdsp
    PUBLIC ${SPEEXDSP_FOLDER}/include 
    PRIVATE ${SPEEXDSP_FOLDER} ${SPEEXDSP_SOURCE} ${SPEEXDSP_FOLDER}/include
    PRIVATE ${SPEEXDSP_FOLDER}/include/speex
)

