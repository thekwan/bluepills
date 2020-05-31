cmake_minimum_required(VERSION 3.10)

project(stm32f1-printf)

enable_language(C ASM)
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)

set(CMAKE_C_FLAGS "-mcpu=cortex-m3 -mthumb -Wall -g -O0")

set(STM32CUBE_DIR "/home/deokhwan/Workspace/STM32/f1/STM32CubeF1/")

set(SOURCES
    lib/src/stm32f10x_adc.c
    lib/src/stm32f10x_bkp.c
    lib/src/stm32f10x_can.c
    lib/src/stm32f10x_dma.c
    lib/src/stm32f10x_exti.c
    lib/src/stm32f10x_flash.c
    lib/src/stm32f10x_gpio.c
    lib/src/stm32f10x_i2c.c
    lib/src/stm32f10x_iwdg.c
    lib/src/stm32f10x_lib.c
    lib/src/stm32f10x_nvic.c
    lib/src/stm32f10x_pwr.c
    lib/src/stm32f10x_rcc.c
    lib/src/stm32f10x_rtc.c
    lib/src/stm32f10x_spi.c
    lib/src/stm32f10x_systick.c
    lib/src/stm32f10x_tim.c
    lib/src/stm32f10x_tim1.c
    lib/src/stm32f10x_usart.c
    lib/src/stm32f10x_wwdg.c
    lib/src/cortexm3_macro.S
    lib/src/stm32f10x_vector.c

    main.c
    stm32f10x_it.c
    )

include_directories(
    .
    lib/inc
    )

set(EXECUTABLE ${PROJECT_NAME}.exe)

set(LINKER_SCRIPT "stm32.ld")
#set(CMAKE_EXE_LINKER_FLAGS "-Wl,--gc-sections,-cref")
#set(CMAKE_EXE_LINKER_FLAGS "-Wl,-Map=map.map,--gc-sections -mcpu=cortex-m3 -mthumb -lnosys -lm -lc -specs=nop")
set(CMAKE_EXE_LINKER_FLAGS "-Wl,-Map=map.map,--gc-sections -mcpu=cortex-m3 -mthumb -mfloat-abi=soft -lc -lm -lnosys -specs=nosys.specs -specs=nano.specs")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -L ${CMAKE_SOURCE_DIR}/lib -T${CMAKE_SOURCE_DIR}/${LINKER_SCRIPT}")

add_executable(${EXECUTABLE} ${SOURCES})

set_target_properties(${TARGET_NAME} PROPERTIES LINK_DEPENDS ${LINKER_SCRIPT})

#target_compile_definitions(${EXECUTABLE} PRIVATE
        #-DUSE_HAL_DRIVER
        #-DSTM32F103xB
        #-D__GNUC__
        #        )

        #target_include_directories(${EXECUTABLE} PRIVATE
        #        ${STM32CUBE_DIR}/Drivers/STM32F1xx_HAL_Driver/Inc
        #        ${STM32CUBE_DIR}/Drivers/CMSIS/Device/ST/STM32F1xx/Include
        #        ${STM32CUBE_DIR}/Drivers/CMSIS/Include
        #        ${CMAKE_SOURCE_DIR}/Inc
        #        )

        #target_compile_options(${EXECUTABLE} PUBLIC
        #        -mcpu=cortex-m3
        #        -mthumb
        #        #-mfpu=fpv4-sp-d16  # it is only valid for F4
        #        -mfloat-abi=soft
        #        -mlittle-endian
        #
        #        -fdata-sections
        #        -ffunction-sections
        #
        #        -Wall
        #        -Werror
        #        #-Wstrict-prototypes
        #        -Warray-bounds
        #        -fno-strict-aliasing
        #        -Wno-unused-const-variable
        #        -specs=nano.specs
        #        -specs=nosys.specs
        #
        #        -O0
        #        -g
        #
        #        #$<$<CONFIG:Debug>:-Og>
        #        )

    #target_link_options(${EXECUTABLE} PRIVATE
    #        -T${CMAKE_SOURCE_DIR}/SW4STM32/STM32F103RB_Nucleo/STM32F103VBIx_FLASH.ld
    #        -mcpu=cortex-m3
    #        -mthumb
    #        #-mfpu=fpv4-sp-d16
    #        -mfloat-abi=soft
    #        -specs=nano.specs
    #        -lc
    #        -lm
    #        -lnosys
    #        -Wl,-Map=${PROJECT_NAME}.map,--cref
    #        -Wl,--gc-sections
    #        )

# Print executable size
add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND arm-none-eabi-size ${EXECUTABLE})

# Create hex file
add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND arm-none-eabi-objcopy -O ihex ${EXECUTABLE} ${PROJECT_NAME}.hex
        COMMAND arm-none-eabi-objcopy -O binary ${EXECUTABLE} ${PROJECT_NAME}.bin)
