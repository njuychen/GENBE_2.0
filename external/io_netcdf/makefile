#makefile to build a wrf_io with netCDF

OBJSL   = wrf_io.o field_routines.o module_wrfsi_static.o
OBJS    = $(OBJSL)
CODE    = ext_ncd_get_dom_ti.code ext_ncd_get_var_td.code ext_ncd_get_var_ti.code ext_ncd_put_dom_ti.code ext_ncd_put_var_td.code ext_ncd_put_var_ti.code transpose.code 
FFLAGS  =  $(FCFLAGS) -I$(NETCDFPATH)/include -I../ioapi_share
LIBS    = -L$(NETCDFPATH)/lib -lnetcdf
CPP1    = $(CPP) $(TRADFLAG)
M4      = m4 -Uinclude -Uindex -Ulen
AR      = ar

.SUFFIXES:      .F90 .f .o .code

all : libwrfio_nf.a 

libwrfio_nf.a:         $(OBJS) $(CODE)
			/bin/rm -f $@
			if [ "$(AR)" != "lib.exe" ] ; then \
                          $(AR) cr libwrfio_nf.a $(OBJSL) ; \
                        else \
                          $(AR) /out:libwrfio_nf.a $(OBJSL) ; \
                        fi
			$(RANLIB) $@

wrf_io.o:               wrf_io.F90 $(CODE)
			grep nf_format_64bit $(NETCDFPATH)/include/netcdf.inc ;\
                        a=$$? ; export a ; \
			if [ $$a -a "$$WRFIO_NCD_LARGE_FILE_SUPPORT" = "1" ] ; then \
                          $(CPP1) -DWRFIO_NCD_LARGE_FILE_SUPPORT -I../ioapi_share wrf_io.F90 | $(M4) - > wrf_io.f ; \
                        else \
                          $(CPP1) -I../ioapi_share wrf_io.F90 | $(M4) - > wrf_io.f ; \
                        fi
			$(FC) -o $@ $(FFLAGS) -c wrf_io.f


module_wrfsi_static.o:  module_wrfsi_static.F90
			$(CPP1) -I../ioapi_share module_wrfsi_static.F90 > module_wrfsi_static.f
			$(FC) -o $@ $(FFLAGS) -c module_wrfsi_static.f

field_routines.o:	field_routines.F90
			$(CPP1) -I../ioapi_share field_routines.F90 > field_routines.f
			$(FC) -o $@ $(FFLAGS) -c field_routines.f

superclean:
			/bin/rm -f *.f *.o *.obj *.i testWRFWrite testWRFRead \
			*.mod libwrfio_nf.a diffwrf
