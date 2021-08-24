
CASE := test-tiny.cfg
#CASE := test-tiny-long.cfg
#CASE := test-big.cfg
#CASE := test-topo.cfg
FRAME := 4
EXE := ../dynearthsol2d
OMP := 1
TFLAG = #-v
DEBUG := 0
NSYS := 0
FUNC := compute_mass
ifeq ($(DEBUG), 1)
	DEBUGFLAG = valgrind --tool=helgrind --log-file=valgrind-out.txt -v
endif
ifeq ($(NSYS), 1)
	DEBUGFLAG = nsys profile --output=benchmark.${CASE}
endif

# Use single cpu for omp to avoid round-off errors
RUN_TEST = rm -f benchmark.*; OMP_NUM_THREADS=${OMP}; time ${TFLAG} ${DEBUGFLAG} ${EXE} ${CASE}; echo

ORIG = orig-${CASE}
CURR = ~/data/jobs/${CASE}

all: cmp

set:
	# run test, store test output to old/
	${RUN_TEST}
	${MAKE} store

cmp:
	${RUN_TEST}
	python3 compare.py ${ORIG} ${FRAME}
#	gprof ../dynearthsol2d gmon.out > benchmark.gp
#	@echo "  %   cumulative   self                self     total"
#	@echo " time   seconds   seconds    calls    ms/call  ms/call  name"
#	@head benchmark.gp -n100 | egrep "compute_mass|update_force|update_temperature|compute_dvoldt" | sed "s/(/ /g" | awk '{printf "%.2f\t %.2f\t   %.2f\t     %5d\t%.2f\t%.2f\t%s\n", $$1,$$2,$$3,$$4,$$5,$$6,$$7}'
#	head benchmark.gp | egrep update_force

store:
	rm -rf ${ORIG}
	mkdir -p ${ORIG}
	mv benchmark.* ${ORIG}
	cp ../snapshot.diff ${ORIG}

setjob:
	# st job to compare
	rm -rf orig-job
	ln -s ~/data/jobs/${CASE} orig-job

cmpjob:
	python3 compare.py orig-job ${CURR} ${FRAME}

vtk:
	python ../2vtk.py -m  benchmark

.PHONY: all set cmp store