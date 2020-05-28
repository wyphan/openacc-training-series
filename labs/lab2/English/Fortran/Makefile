# Pick your compile options here
FCOPTS = -fast -ta=tesla -Minfo=accel

# Change this to "ORNL" or "NERSC"
LOCATION = NERSC

# Insert your OLCF/NERSC project ID here, then uncomment the line
PROJECT = m3502

.PHONY: all
all: clean laplace run

laplace:
        pgfortran $(FCOPTS) -c laplace2d.f90
        pgfortran $(FCOPTS) -c jacobi.f90
        pgfortran $(FCOPTS) -o laplace.x jacobi.o laplace2d.o

clean:
        -rm jacobi.mod jacobi.o laplace2d.o
        -rm laplace.x

chkalloc:
ifndef PROJECT
        $(error Please edit the Makefile and provide your allocation/project ID)
else
        @echo "Using project $(PROJECT)"
endif

ifeq ( $(LOCATION), "ORNL" )
LSFRUN = bsub -W 5 -nnodes 1 -P $(PROJECT) -Is jsrun -n1 -a1 -c10 -g1 --smpiargs="none"
else ( $(LOCATION), "NERSC" )
LSFRUN = srun -C gpu -N 1 -n 1 -t 5 -A $(PROJECT) -c 10 --gres=gpu:1
endif

pgaccelinfo: chkalloc
        $(LSFRUN) pgaccelinfo

run: chkalloc
        $(LSFRUN) ./laplace.x

profile: chkalloc
        $(LSFRUN) nv-nsight-cu-cli ./laplace.x
