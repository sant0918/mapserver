add_library(mod_mapserver MODULE mod_mapserver.c)
target_link_libraries(mod_mapserver ${MAPSERVER_LIBMAPSERVER})
set_target_properties(mod_mapserver PROPERTIES PREFIX "")

IF(APPLE)
  if(HTTPD_EXECUTABLE)
    if(XCODE)
       SET(CMAKE_C_LINK_FLAGS 
          "${CMAKE_C_LINK_FLAGS} -bundle_loader ${HTTPD_EXECUTABLE}")
    else(XCODE)
       SET(CMAKE_SHARED_MODULE_CREATE_C_FLAGS 
          "${CMAKE_SHARED_MODULE_CREATE_C_FLAGS} -bundle_loader ${HTTPD_EXECUTABLE}")
    endif(XCODE)
  else(HTTPD_EXECUTABLE)
    if(XCODE)
       SET(CMAKE_C_LINK_FLAGS 
          "${CMAKE_C_LINK_FLAGS} -undefined dynamic_lookup")
    else(XCODE)
       SET(CMAKE_SHARED_MODULE_CREATE_C_FLAGS 
          "${CMAKE_SHARED_MODULE_CREATE_C_FLAGS} -Wl,-undefined,dynamic_lookup")
    endif(XCODE)
  endif(HTTPD_EXECUTABLE)
ENDIF(APPLE)

find_package(APACHE)
if(APACHE_FOUND)
   include_directories(${APACHE_INCLUDE_DIR})
else(APACHE_FOUND)
   report_mandatory_not_found(APACHE)
endif(APACHE_FOUND)

find_package(APR)
if(APR_FOUND)
   include_directories(${APR_INCLUDE_DIR})
   target_link_libraries(mod_mapserver ${APR_LIBRARY})
   if(DEFINED APR_CPPFLAGS)
     add_definitions("${APR_CPPFLAGS}")
   endif(DEFINED APR_CPPFLAGS)
else(APR_FOUND)
   report_mandatory_not_found(APR)
endif(APR_FOUND)

if(APACHE_MODULE_DIR)
  INSTALL(TARGETS mod_mapserver DESTINATION ${APACHE_MODULE_DIR})
endif(APACHE_MODULE_DIR)
