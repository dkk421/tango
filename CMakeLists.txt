cmake_minimum_required(VERSION 3.0)

# Имя проекта
project(Test_Device)

# Получаем путь к TANGO из переменной окружения
if(DEFINED ENV{TANGO_ROOT})
    set(TANGO_ROOT $ENV{TANGO_ROOT})
else()
    set(TANGO_ROOT "C:/Program Files/tango")
endif()

message(STATUS "Using TANGO_ROOT: ${TANGO_ROOT}")

# Пути к заголовочным файлам
set(TANGO_INCLUDE_DIRS 
    "${TANGO_ROOT}/win64/include"
    "${TANGO_ROOT}/win64/include/tango"
)

# Путь к библиотекам (64-bit)
set(TANGO_LIB_DIR "${TANGO_ROOT}/win64/lib")

# Добавляем пути для включаемых файлов
include_directories(${TANGO_INCLUDE_DIRS})

# Добавляем путь к библиотекам
link_directories(${TANGO_LIB_DIR})

# Список исходных файлов
set(SOURCES
    ClassFactory.cpp
    main.cpp
    Test_Device.cpp
    Test_DeviceClass.cpp
    Test_DeviceStateMachine.cpp
)

# Создаем исполняемый файл
add_executable(${PROJECT_NAME} ${SOURCES})

# Линкуем с библиотеками (имена могут отличаться, нужно проверить реальные имена файлов в вашей lib папке)
target_link_libraries(${PROJECT_NAME}
    tango
    omniORB4_rt
    omniDynamic4_rt
    omnithread_rt
    COS4_rt
)

# Свойства компиляции
set_target_properties(${PROJECT_NAME} PROPERTIES
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED YES
    CXX_EXTENSIONS NO
)

if(WIN32)
    target_compile_definitions(${PROJECT_NAME} PRIVATE
        WIN32
        _WINDOWS
        _CRT_SECURE_NO_WARNINGS
        TANGO_HAS_DLL
        _WIN32_WINNT=0x0601
    )
endif()

# Копирование исполняемого файла в папку bin после сборки
install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION "${TANGO_ROOT}/bin"
)