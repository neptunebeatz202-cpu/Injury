cmake_minimum_required(VERSION 3.22)

project(Injury VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ── JUCE via FetchContent ──────────────────────────────────────────────────────
include(FetchContent)
FetchContent_Declare(
    JUCE
    GIT_REPOSITORY https://github.com/juce-framework/JUCE.git
    GIT_TAG        7.0.12
    GIT_SHALLOW    TRUE
)
FetchContent_MakeAvailable(JUCE)

# This line is the crucial fix for the "target not found" error.
# It makes the recommended_warning_flags and recommended_config_flags visible.
include(${juce_SOURCE_DIR}/extras/Build/CMake/JUCEUtils.cmake)

# ── Plugin target ──────────────────────────────────────────────────────────────
juce_add_plugin(Injury
    COMPANY_NAME            "Injury Audio"
    BUNDLE_ID               "com.injuryaudio.injury" # Fixed space to prevent warnings
    PLUGIN_MANUFACTURER_CODE InjA
    PLUGIN_CODE             Inj1
    FORMATS                 VST3 AU Standalone
    PRODUCT_NAME            "Injury"
    IS_SYNTH                TRUE
    NEEDS_MIDI_INPUT        TRUE
    NEEDS_MIDI_OUTPUT       FALSE
    IS_MIDI_EFFECT          FALSE
    EDITOR_WANTS_KEYBOARD_FOCUS FALSE
    VST3_CATEGORIES         "Instrument|Synth"
    AU_MAIN_TYPE            kAudioUnitType_MusicDevice
    COPY_PLUGIN_AFTER_BUILD FALSE
)

# ── Embedded binary resources ──────────────────────────────────────────────────
juce_add_binary_data(InjuryBinaryData
    SOURCES
        Resources/background.jpg
)

# ── Sources ────────────────────────────────────────────────────────────────────
target_sources(Injury PRIVATE
    Source/PluginProcessor.cpp
    Source/PluginEditor.cpp
    Source/WavetableEngine.cpp
    Source/SynthVoice.cpp
    Source/SampleAnalyzer.cpp
    Source/WaveformDisplay.cpp
    Source/ADSRVisualizer.cpp
    Source/LookAndFeel.cpp
    Source/FxProcessor.cpp
)

# ── Compile definitions ────────────────────────────────────────────────────────
target_compile_definitions(Injury PUBLIC
    JUCE_WEB_BROWSER=0
    JUCE_USE_CURL=0
    JUCE_VST3_CAN_REPLACE_VST2=0
    JUCE_DISPLAY_SPLASH_SCREEN=0
    JUCE_MODAL_LOOPS_PERMITTED=1
)

# ── JUCE modules + binary data ────────────────────────────────────────────────
target_link_libraries(Injury
    PRIVATE
        juce::juce_audio_basics
        juce::juce_audio_devices
        juce::juce_audio_formats
        juce::juce_audio_plugin_client
        juce::juce_audio_processors
        juce::juce_audio_utils
        juce::juce_core
        juce::juce_data_structures
        juce::juce_dsp
        juce::juce_events
        juce::juce_graphics
        juce::juce_gui_basics
        juce::juce_gui_extra
        InjuryBinaryData
    PUBLIC
        juce::juce_recommended_config_flags
        juce::juce_recommended_warning_flags
)

