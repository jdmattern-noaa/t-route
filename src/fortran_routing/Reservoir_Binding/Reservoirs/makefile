NETCDFINC       :=       $(NETCDF_INC)
NETCDFLIB       =       -L$(NETCDF_LIB) -lnetcdff -lnetcdf
COMPILER90 = gfortran
F90FLAGS = -w -c -ffree-form -ffree-line-length-none -fconvert=big-endian -frecord-marker=4
F90FLAGS := -g $(F90FLAGS) -I$(NETCDFINC) -static-libgfortran -static-libgcc -nodefaultlibs -lgfortran -lgcc -fPIC
AR = ar
ARFLAGS = rc

VPATH = ./Level_Pool ./Persistence_Level_Pool_Hybrid ./RFC_Forecasts

.PHONY: all install install_lp

all: binding_lp.a bind_hybrid.a bind_rfc.a

test_main: test_main.o binding_lp.a bind_hybrid.a bind_rfc.a
	gfortran -g -o $@ $^

test_main.o: test_binding.c
	gcc -g -c -o $@ $<

module_levelpool.o: module_reservoir.o module_levelpool_properties.o module_levelpool_state.o

module_persistence_levelpool_hybrid.o: module_reservoir.o module_hydro_stop.o module_reservoir_utilities.o module_persistence_levelpool_hybrid_properties.o module_persistence_levelpool_hybrid_state.o module_levelpool.o module_reservoir_read_timeslice_data.o

module_rfc_forecasts.o: module_reservoir.o module_hydro_stop.o module_reservoir_utilities.o module_rfc_forecasts_properties.o module_rfc_forecasts_state.o module_levelpool.o module_reservoir_read_rfc_time_series_data.o

bind_lp.o: module_levelpool.o

bind_hybrid.o: module_persistence_levelpool_hybrid.o

bind_rfc.o: module_rfc_forecasts.o

binding_lp.a: module_reservoir.o module_levelpool_properties.o module_levelpool_state.o module_levelpool.o bind_lp.o
	$(AR) $(ARFLAGS) $@ $^

bind_hybrid.a: module_reservoir.o module_hydro_stop.o module_reservoir_utilities.o module_persistence_levelpool_hybrid_properties.o module_persistence_levelpool_hybrid_state.o module_levelpool_properties.o module_levelpool_state.o module_levelpool.o module_reservoir_read_timeslice_data.o module_persistence_levelpool_hybrid.o bind_hybrid.o
	$(AR) $(ARFLAGS) $@ $^

bind_rfc.a: module_reservoir.o module_hydro_stop.o module_reservoir_utilities.o module_rfc_forecasts_properties.o module_rfc_forecasts_state.o module_levelpool_properties.o module_levelpool_state.o module_levelpool.o module_reservoir_read_rfc_time_series_data.o module_rfc_forecasts.o bind_rfc.o
	$(AR) $(ARFLAGS) $@ $^

%.o: %.F
	$(COMPILER90) $(F90FLAGS) $(NETCDFLIB) -c -o $@ $<

%.o: %.f90
	$(COMPILER90) $(F90FLAGS) $(NETCDFLIB) -c -o $@ $<

install: binding_lp.a bind_hybrid.a bind_rfc.a
	echo $^
	for lib in $^; do \
		cp $$lib ../../../python_framework_v02/libs; \
	done

install_lp: binding_lp.a
	cp $< ../../../python_framework_v02/libs;

clean:
	rm -f *.o
	rm -f *.mod
	rm -f *.a
	rm -f reservoir_tests
	rm -f test_main
