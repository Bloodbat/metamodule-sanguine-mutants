cmake_minimum_required(VERSION 3.24)

if (DEFINED ENV{METAMODULE_SDK_DIR})
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
elseif("${METAMODULE_SDK_DIR}" STREQUAL "")
    set(METAMODULE_SDK_DIR "${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugin-sdk")
endif()
include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(SanguineMutants VERSION 2.5.2 DESCRIPTION "Sanguine Mutants for Rack" LANGUAGES C CXX)

# Set C++17 standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Define source directories
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/SanguineMutants)
set(ALT_FIRMWARE_DIR ${SOURCE_DIR}/alt_firmware)
set(EURORACK_DIR ${SOURCE_DIR}/eurorack)

add_library(SanguineMutants STATIC)

target_compile_definitions(SanguineMutants PRIVATE 
    __V1x 
    __PLUGIN
    NOASM
)

# Add compile options for better control over symbols
target_compile_options(SanguineMutants PRIVATE
    -fdata-sections
    -ffunction-sections
    -fno-exceptions
    -fno-threadsafe-statics
    -fno-use-cxa-atexit
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility=hidden>
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility-inlines-hidden>
)

# Add linker flags for static linking
target_link_options(SanguineMutants PRIVATE
    -Wl,--gc-sections
    -static-libgcc
    -static-libstdc++
)

# Add source files
target_sources(SanguineMutants PRIVATE
    ${SOURCE_DIR}/src/plugin.cpp
    ${SOURCE_DIR}/src/sanguinecomponents.cpp
    ${SOURCE_DIR}/src/themes.cpp
    ${SOURCE_DIR}/src/sanguinehelpers.cpp
    ${EURORACK_DIR}/warps/dsp/modulator.cc
    ${EURORACK_DIR}/warps/dsp/filter_bank.cc
    ${EURORACK_DIR}/warps/dsp/oscillator.cc
    ${EURORACK_DIR}/warps/dsp/vocoder.cc
    ${EURORACK_DIR}/warps/resources.cc
    ${EURORACK_DIR}/stmlib/dsp/units.cc
    ${EURORACK_DIR}/stmlib/utils/random.cc
    ${EURORACK_DIR}/stmlib/dsp/atan.cc
    ${ALT_FIRMWARE_DIR}/parasites_stmlib/dsp/parasites_atan.cc  
    
    # Add Clouds Parasite source files
    ${ALT_FIRMWARE_DIR}/clouds_parasite/dsp/etesia_mu_law.cc
    ${ALT_FIRMWARE_DIR}/clouds_parasite/dsp/etesia_correlator.cc
    ${ALT_FIRMWARE_DIR}/clouds_parasite/dsp/etesia_granular_processor.cc
    ${ALT_FIRMWARE_DIR}/clouds_parasite/dsp/pvoc/etesia_frame_transformation.cc
    ${ALT_FIRMWARE_DIR}/clouds_parasite/dsp/pvoc/etesia_phase_vocoder.cc
    ${ALT_FIRMWARE_DIR}/clouds_parasite/etesia_resources.cc
    ${ALT_FIRMWARE_DIR}/clouds_parasite/dsp/pvoc/etesia_frame_transformation.cc
    ${ALT_FIRMWARE_DIR}/clouds_parasite/dsp/pvoc/etesia_stft.cc
    
    # Add other existing source files...
    ${ALT_FIRMWARE_DIR}/distortiones/dsp/distortiones_modulator.cc
    ${ALT_FIRMWARE_DIR}/mutuus/dsp/mutuus_modulator.cc
    # Add mutuus source files
    ${ALT_FIRMWARE_DIR}/mutuus/dsp/mutuus_filter_bank.cc
    ${ALT_FIRMWARE_DIR}/mutuus/dsp/mutuus_oscillator.cc
    ${ALT_FIRMWARE_DIR}/mutuus/dsp/mutuus_vocoder.cc
    ${ALT_FIRMWARE_DIR}/mutuus/mutuus_resources.cc
    # Add distortiones source files  
    ${ALT_FIRMWARE_DIR}/distortiones/dsp/distortiones_filter_bank.cc
    ${ALT_FIRMWARE_DIR}/distortiones/dsp/distortiones_oscillator.cc
    ${ALT_FIRMWARE_DIR}/distortiones/dsp/distortiones_vocoder.cc
    ${ALT_FIRMWARE_DIR}/distortiones/distortiones_resources.cc
    
    # Add parasites_stmlib source files
    ${ALT_FIRMWARE_DIR}/parasites_stmlib/dsp/parasites_units.cc
    ${ALT_FIRMWARE_DIR}/parasites_stmlib/utils/parasites_random.cc

    # Add deadman source files
    ${ALT_FIRMWARE_DIR}/deadman/deadman_resources.cc
    ${ALT_FIRMWARE_DIR}/deadman/deadman_processors.cc
    ${ALT_FIRMWARE_DIR}/deadman/pulse_processor/deadman_pulse_shaper.cc
    ${ALT_FIRMWARE_DIR}/deadman/pulse_processor/deadman_pulse_randomizer.cc
    ${ALT_FIRMWARE_DIR}/deadman/modulations/deadman_multistage_envelope.cc
    ${ALT_FIRMWARE_DIR}/deadman/modulations/deadman_lfo.cc
    ${ALT_FIRMWARE_DIR}/deadman/number_station/deadman_bytebeats.cc
    ${ALT_FIRMWARE_DIR}/deadman/number_station/deadman_number_station.cc
    ${ALT_FIRMWARE_DIR}/deadman/drums/deadman_cymbal.cc
    ${ALT_FIRMWARE_DIR}/deadman/drums/deadman_fm_drum.cc
    ${ALT_FIRMWARE_DIR}/deadman/drums/deadman_high_hat.cc
    ${ALT_FIRMWARE_DIR}/deadman/drums/deadman_snare_drum.cc
    ${ALT_FIRMWARE_DIR}/deadman/drums/deadman_bass_drum.cc

    # Add fluctus source files
    ${ALT_FIRMWARE_DIR}/fluctus/fluctus_resources.cc
    ${ALT_FIRMWARE_DIR}/fluctus/dsp/fluctus_granular_processor.cc
    ${ALT_FIRMWARE_DIR}/fluctus/dsp/fluctus_kammerl_player.cc
    ${ALT_FIRMWARE_DIR}/fluctus/dsp/fluctus_mu_law.cc
    ${ALT_FIRMWARE_DIR}/fluctus/dsp/fluctus_correlator.cc
    ${ALT_FIRMWARE_DIR}/fluctus/dsp/pvoc/fluctus_stft.cc
    ${ALT_FIRMWARE_DIR}/fluctus/dsp/pvoc/fluctus_phase_vocoder.cc
    ${ALT_FIRMWARE_DIR}/fluctus/dsp/pvoc/fluctus_spectral_clouds_transformation.cc

    # Add bumps source files
    ${ALT_FIRMWARE_DIR}/bumps/bumps_resources.cc
    ${ALT_FIRMWARE_DIR}/bumps/bumps_generator.cc

    # Add tides source files
    ${EURORACK_DIR}/tides/resources.cc
    ${EURORACK_DIR}/tides/generator.cc

    # Add tides2 source files
    ${EURORACK_DIR}/tides2/resources.cc
    ${EURORACK_DIR}/tides2/poly_slope_generator.cc
    ${EURORACK_DIR}/tides2/ramp/ramp_extractor.cc

    # Add rings source files
    ${EURORACK_DIR}/rings/resources.cc
    ${EURORACK_DIR}/rings/dsp/resonator.cc
    ${EURORACK_DIR}/rings/dsp/string.cc
    ${EURORACK_DIR}/rings/dsp/string_synth_part.cc
    ${EURORACK_DIR}/rings/dsp/part.cc
    ${EURORACK_DIR}/rings/dsp/fm_voice.cc

    # Add marbles source files
    ${EURORACK_DIR}/marbles/resources.cc
    ${EURORACK_DIR}/marbles/random/t_generator.cc
    ${EURORACK_DIR}/marbles/random/x_y_generator.cc
    ${EURORACK_DIR}/marbles/random/lag_processor.cc
    ${EURORACK_DIR}/marbles/random/output_channel.cc
    ${EURORACK_DIR}/marbles/random/quantizer.cc
    ${EURORACK_DIR}/marbles/ramp/ramp_extractor.cc

    # Add clouds source files
    ${EURORACK_DIR}/clouds/resources.cc
    ${EURORACK_DIR}/clouds/dsp/mu_law.cc
    ${EURORACK_DIR}/clouds/dsp/correlator.cc
    ${EURORACK_DIR}/clouds/dsp/granular_processor.cc
    ${EURORACK_DIR}/clouds/dsp/pvoc/frame_transformation.cc
    ${EURORACK_DIR}/clouds/dsp/pvoc/phase_vocoder.cc
    ${EURORACK_DIR}/clouds/dsp/pvoc/stft.cc

    # Add peaks source files
    ${EURORACK_DIR}/peaks/resources.cc
    ${EURORACK_DIR}/peaks/processors.cc
    ${EURORACK_DIR}/peaks/number_station/number_station.cc
    ${EURORACK_DIR}/peaks/pulse_processor/pulse_randomizer.cc
    ${EURORACK_DIR}/peaks/pulse_processor/pulse_shaper.cc
    ${EURORACK_DIR}/peaks/drums/snare_drum.cc
    ${EURORACK_DIR}/peaks/drums/bass_drum.cc
    ${EURORACK_DIR}/peaks/drums/fm_drum.cc
    ${EURORACK_DIR}/peaks/drums/high_hat.cc
    ${EURORACK_DIR}/peaks/modulations/lfo.cc
    ${EURORACK_DIR}/peaks/modulations/multistage_envelope.cc

    # Add plaits source files
    ${EURORACK_DIR}/plaits/resources.cc
    ${EURORACK_DIR}/plaits/dsp/voice.cc
    ${EURORACK_DIR}/plaits/dsp/chords/chord_bank.cc
    ${EURORACK_DIR}/plaits/dsp/speech/lpc_speech_synth_phonemes.cc
    ${EURORACK_DIR}/plaits/dsp/speech/lpc_speech_synth_words.cc
    ${EURORACK_DIR}/plaits/dsp/speech/naive_speech_synth.cc
    ${EURORACK_DIR}/plaits/dsp/speech/sam_speech_synth.cc
    ${EURORACK_DIR}/plaits/dsp/speech/lpc_speech_synth.cc
    ${EURORACK_DIR}/plaits/dsp/speech/lpc_speech_synth_controller.cc
    ${EURORACK_DIR}/plaits/dsp/physical_modelling/resonator.cc
    ${EURORACK_DIR}/plaits/dsp/physical_modelling/string.cc
    ${EURORACK_DIR}/plaits/dsp/physical_modelling/string_voice.cc
    ${EURORACK_DIR}/plaits/dsp/physical_modelling/modal_voice.cc
    ${EURORACK_DIR}/plaits/dsp/engine/speech_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/string_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/swarm_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/virtual_analog_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/waveshaping_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/wavetable_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/hi_hat_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/modal_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/noise_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/particle_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/snare_drum_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/additive_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/bass_drum_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/chord_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/fm_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine/grain_engine.cc

    # Add plaits engine2 source files
    ${EURORACK_DIR}/plaits/dsp/engine2/wave_terrain_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine2/chiptune_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine2/phase_distortion_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine2/six_op_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine2/string_machine_engine.cc
    ${EURORACK_DIR}/plaits/dsp/engine2/virtual_analog_vcf_engine.cc
    ${EURORACK_DIR}/plaits/dsp/fm/algorithms.cc
    ${EURORACK_DIR}/plaits/dsp/fm/dx_units.cc

    ${ALT_FIRMWARE_DIR}/renaissance/renaissance_resources.cc
    ${ALT_FIRMWARE_DIR}/renaissance/renaissance_quantizer.cc
    ${ALT_FIRMWARE_DIR}/renaissance/renaissance_macro_oscillator.cc
    ${ALT_FIRMWARE_DIR}/renaissance/renaissance_analog_oscillator.cc
    ${ALT_FIRMWARE_DIR}/renaissance/renaissance_digital_oscillator.cc
    ${ALT_FIRMWARE_DIR}/renaissance/renaissance_harmonics.cc
    ${ALT_FIRMWARE_DIR}/renaissance/renaissance_stack.cc

    ${ALT_FIRMWARE_DIR}/renaissance/vocalist/vocalist.cc
    ${ALT_FIRMWARE_DIR}/renaissance/vocalist/sam.cc
    ${ALT_FIRMWARE_DIR}/renaissance/vocalist/wordlist.cc
    ${ALT_FIRMWARE_DIR}/renaissance/vocalist/rendertabs.cc
    
    ${EURORACK_DIR}/braids/macro_oscillator.cc
    ${EURORACK_DIR}/braids/resources.cc
    ${EURORACK_DIR}/braids/analog_oscillator.cc
    ${EURORACK_DIR}/braids/quantizer.cc
    ${EURORACK_DIR}/braids/digital_oscillator.cc

    ${ALT_FIRMWARE_DIR}/scalaria/dsp/scalaria_modulator.cc
    ${ALT_FIRMWARE_DIR}/scalaria/dsp/scalaria_oscillator.cc
    ${ALT_FIRMWARE_DIR}/scalaria/scalaria_resources.cc

    # Parasites and base firmware
    ${SOURCE_DIR}/src/incurvationes.cpp
    ${SOURCE_DIR}/src/distortiones.cpp
    ${SOURCE_DIR}/src/mutuus.cpp
    ${SOURCE_DIR}/src/contextus.cpp
    ${SOURCE_DIR}/src/nodi.cpp
    ${SOURCE_DIR}/src/nodicommon.cpp
    ${SOURCE_DIR}/src/etesia.cpp
    ${SOURCE_DIR}/src/mortuus.cpp
    ${SOURCE_DIR}/src/fluctus.cpp
    ${SOURCE_DIR}/src/explorator.cpp
    ${SOURCE_DIR}/src/temulenti.cpp
    ${SOURCE_DIR}/src/vimina.cpp
    ${SOURCE_DIR}/src/aestus.cpp
    ${SOURCE_DIR}/src/velamina.cpp
    ${SOURCE_DIR}/src/anuli.cpp
    ${SOURCE_DIR}/src/marmora.cpp
    ${SOURCE_DIR}/src/nebulae.cpp
    ${SOURCE_DIR}/src/aleae.cpp
    ${SOURCE_DIR}/src/apices.cpp
    ${SOURCE_DIR}/src/funes.cpp
    ${SOURCE_DIR}/src/ansa.cpp
    ${SOURCE_DIR}/src/nix.cpp
    ${SOURCE_DIR}/src/scalaria.cpp
    ${SOURCE_DIR}/src/mutantsblank.cpp

)

# Add include directories
target_include_directories(SanguineMutants PRIVATE 
    ${SOURCE_DIR}
    ${SOURCE_DIR}/parasites_stmlib
    ${SOURCE_DIR}/dsp
    ${SOURCE_DIR}/src
    ${SOURCE_DIR}/pcgcpp
    ${EURORACK_DIR}
    ${ALT_FIRMWARE_DIR}
)

# Create the plugin file
create_plugin(
    SOURCE_LIB      SanguineMutants
    PLUGIN_NAME     SanguineMutants
    PLUGIN_JSON     ${SOURCE_DIR}/plugin.json
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets
    DESTINATION     ${CMAKE_CURRENT_LIST_DIR}/metamodule-plugins
)