include_directories("../")

set(CORE_SRC
  "router/context.cc"
  "router/garlic.cc"
  "router/i2np.cc"
  "router/identity.cc"
  "router/info.cc"
  "router/lease_set.cc"
  "router/net_db/impl.cc"
  "router/net_db/requests.cc"
  "router/profiling.cc"
  "router/transports/impl.cc"
  "router/transports/ntcp/server.cc"
  "router/transports/ntcp/session.cc"
  "router/transports/ssu/data.cc"
  "router/transports/ssu/packet.cc"
  "router/transports/ssu/server.cc"
  "router/transports/ssu/session.cc"
  "router/transports/upnp.cc"
  "router/tunnel/config.cc"
  "router/tunnel/endpoint.cc"
  "router/tunnel/gateway.cc"
  "router/tunnel/impl.cc"
  "router/tunnel/pool.cc"
  "router/tunnel/transit.cc"
  "util/base64.cc"
  "util/byte_stream.cc"
  "util/exception.cc"
  "util/filesystem.cc"
  "util/log.cc"
  "util/mtu.cc")

if(CMAKE_SYSTEM_NAME STREQUAL "Windows")
  list(APPEND CORE_SRC "util/i2p_endian.cc")
endif()

if(ANDROID)
  list(APPEND CORE_SRC "../../deps/webrtc/base/ifaddrs-android.cc")
  set_source_files_properties(../../deps/webrtc/base/ifaddrs-android.cc PROPERTIES COMPILE_FLAGS -DWEBRTC_ANDROID)
endif()

if(WITH_CRYPTOPP)
  set(CRYPTOPP_DIR "crypto/impl/cryptopp")
endif()

set(CRYPTO_SRC
  "${CRYPTOPP_DIR}/aes.cc"
  "${CRYPTOPP_DIR}/crypto_const.cc"
  "${CRYPTOPP_DIR}/diffie_hellman.cc"
  "${CRYPTOPP_DIR}/elgamal.cc"
  "${CRYPTOPP_DIR}/hash.cc"
  "${CRYPTOPP_DIR}/rand.cc"
  "${CRYPTOPP_DIR}/signature.cc"
  "${CRYPTOPP_DIR}/tunnel.cc"
  "${CRYPTOPP_DIR}/util/checksum.cc"
  "${CRYPTOPP_DIR}/util/compression.cc"
  "${CRYPTOPP_DIR}/util/x509.cc")

if(WITH_SUPERCOP)
  set(EDDSA_DIR "crypto/impl/supercop")
  add_subdirectory("crypto/impl/supercop/ed25519")
endif()

set(EDDSA_SRC
  "${EDDSA_DIR}/signature.cc"
  "${EDDSA_DIR}/ed25519/fe_0.cc"
  "${EDDSA_DIR}/ed25519/fe_1.cc"
  "${EDDSA_DIR}/ed25519/fe_add.cc"
  "${EDDSA_DIR}/ed25519/fe_cmov.cc"
  "${EDDSA_DIR}/ed25519/fe_copy.cc"
  "${EDDSA_DIR}/ed25519/fe_frombytes.cc"
  "${EDDSA_DIR}/ed25519/fe_invert.cc"
  "${EDDSA_DIR}/ed25519/fe_isnegative.cc"
  "${EDDSA_DIR}/ed25519/fe_isnonzero.cc"
  "${EDDSA_DIR}/ed25519/fe_mul.cc"
  "${EDDSA_DIR}/ed25519/fe_neg.cc"
  "${EDDSA_DIR}/ed25519/fe_pow22523.cc"
  "${EDDSA_DIR}/ed25519/fe_sq.cc"
  "${EDDSA_DIR}/ed25519/fe_sq2.cc"
  "${EDDSA_DIR}/ed25519/fe_sub.cc"
  "${EDDSA_DIR}/ed25519/fe_tobytes.cc"
  "${EDDSA_DIR}/ed25519/ge_add.cc"
  "${EDDSA_DIR}/ed25519/ge_double_scalarmult.cc"
  "${EDDSA_DIR}/ed25519/ge_frombytes.cc"
  "${EDDSA_DIR}/ed25519/ge_madd.cc"
  "${EDDSA_DIR}/ed25519/ge_msub.cc"
  "${EDDSA_DIR}/ed25519/ge_p1p1_to_p2.cc"
  "${EDDSA_DIR}/ed25519/ge_p1p1_to_p3.cc"
  "${EDDSA_DIR}/ed25519/ge_p2_0.cc"
  "${EDDSA_DIR}/ed25519/ge_p2_dbl.cc"
  "${EDDSA_DIR}/ed25519/ge_p3_0.cc"
  "${EDDSA_DIR}/ed25519/ge_p3_dbl.cc"
  "${EDDSA_DIR}/ed25519/ge_p3_to_cached.cc"
  "${EDDSA_DIR}/ed25519/ge_p3_to_p2.cc"
  "${EDDSA_DIR}/ed25519/ge_p3_tobytes.cc"
  "${EDDSA_DIR}/ed25519/ge_precomp_0.cc"
  "${EDDSA_DIR}/ed25519/ge_scalarmult_base.cc"
  "${EDDSA_DIR}/ed25519/ge_sub.cc"
  "${EDDSA_DIR}/ed25519/ge_tobytes.cc"
  "${EDDSA_DIR}/ed25519/keypair.cc"
  "${EDDSA_DIR}/ed25519/open.cc"
  "${EDDSA_DIR}/ed25519/sc_muladd.cc"
  "${EDDSA_DIR}/ed25519/sc_reduce.cc"
  "${EDDSA_DIR}/ed25519/sign.cc"
  "${EDDSA_DIR}/ed25519/verify.cc")

# Library building
if(WITH_LIBRARY)
  add_library(${CORE_NAME} ${CORE_SRC} ${CRYPTO_SRC} ${EDDSA_SRC})
  target_link_libraries(
    ${CORE_NAME}
    ${Boost_LIBRARIES} ${CryptoPP_LIBRARIES} ${OPENSSL_LIBRARIES}
    ${CPPNETLIB_LIBRARIES} ${DL_LIB})
  install(
    TARGETS ${CORE_NAME}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})
endif()

# vim: noai:ts=2:sw=2
