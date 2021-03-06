#
# Copyright (c) 2018 Nordic Semiconductor
#
# SPDX-License-Identifier: LicenseRef-BSD-5-Clause-Nordic
#

zephyr_interface_library_named(nrfxlib_crypto)

if (CONFIG_NRF_OBERON)
  set(OBERON_BASE ${CMAKE_CURRENT_SOURCE_DIR}/nrf_oberon)
  set(OBERON_VER 2.0.7)
  set(OBERON_LIB ${OBERON_BASE}/${lib_path}/liboberon_${OBERON_VER}.a)
  if(NOT EXISTS ${OBERON_LIB})
    message(WARNING "This combination of SoC and floating point ABI is not supported by the nrf_oberon lib."
                    "(${OBERON_LIB} doesn't exist.)")
  endif()
  target_include_directories(nrfxlib_crypto INTERFACE ${OBERON_BASE}/include)
  target_link_libraries(nrfxlib_crypto INTERFACE ${OBERON_LIB})
endif()

if (CONFIG_NRF_CC310_BL)
  assert(CONFIG_HAS_HW_NRF_CC310 "nrf_cc310_bl requires CryptoCell hardware.")
  set(CC310_BL_BASE ${CMAKE_CURRENT_SOURCE_DIR}/nrf_cc310_bl)
  if (NOT CONFIG_NRF_CC310_BL_INTERRUPTS)
    set(CC310_FLAVOR no-interrupts)
  endif()
  set(CC310_BL_VER 0.9.12)
  set(CC310_BL_LIB ${CC310_BL_BASE}/${lib_path}/${CC310_FLAVOR}/libnrf_cc310_bl_${CC310_BL_VER}.a)
  if(NOT EXISTS ${CC310_BL_LIB})
    message(WARNING "This combination of SoC, floating point ABI, and interrupts settings is not supported by the nrf_cc310_bl lib."
                    "(${CC310_BL_LIB} doesn't exist.)")
  endif()
  target_include_directories(nrfxlib_crypto INTERFACE ${CC310_BL_BASE}/include)
  target_link_libraries(nrfxlib_crypto INTERFACE ${CC310_BL_LIB})
endif()

if (NOT CONFIG_NRF_OBERON AND NOT CONFIG_NRF_CC310_BL)
  assert( "NRFXLIB_CRYPTO enabled, but neither nrf_cc310_bl nor nrf_oberon are enabled.")
endif()
