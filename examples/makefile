# makes the example files

SRC_FILES := $(wildcard *.cpp)
SRC_FILES := $(filter-out distributed.cpp, $(SRC_FILES))
SRC_FILES := $(filter-out distributed_momentum_sgd.cpp, $(SRC_FILES))
OBJ_FILES := $(patsubst %.cpp, %.o, $(SRC_FILES))

TARGETS := $(patsubst %.cpp, %.out, $(SRC_FILES)) #distributed

ifeq ($(USE_MPI),1)
TARGETS += distributed
TARGETS += distributed_momentum_sgd
endif

TESTING_FLAGS := $(OPTIMIZATION_LEVEL) $(WARNINGS) $(CXX_VERSION) $(CUDA_MACRO)
RPATH_FLAGS := -Wl,-rpath,$(prefix)/lib
LIB_PATH := $(prefix)/lib
DEST = ./bin

USE_HDF5 ?= 0
ifeq ($(USE_HDF5),1)
TESTING_FLAGS += -DUSE_HDF5
HDF5DIR ?= /usr/local/hdf5
TESTING_FLAGS += -I$(HDF5DIR)/include
LIBDIRS += -L$(HDF5DIR)/lib
LIBS += -lhdf5 -lhdf5_cpp -lhdf5_hl
endif

ifeq ($(HAS_CUDA),1)
TESTING_FLAGS += -DUSE_GPU
endif

ifeq ($(DEBUG),1)
TESTING_FLAGS += -g -DDEBUG
endif

all: $(DEST) $(TARGETS)

$(DEST):
	mkdir -p $@

%.out: %.o
	$(CXX) $(TESTING_FLAGS) $(RPATH_FLAGS) -o $(DEST)/$(@:.out=) $< -L$(LIB_PATH) -lmagmadnn $(LIBDIRS) $(LIBS)

%.o: %.cpp
	$(CXX) $(TESTING_FLAGS) -o $@ -c $< $(INC) -I../include

distributed: distributed.cpp
	mpicxx $(TESTING_FLAGS) $(RPATH_FLAGS) -o ./bin/$@ $< $(INC) -I../include -D_HAS_MPI_ -L$(LIB_PATH) -lmagmadnn $(LIBDIRS) $(LIBS)

distributed_momentum_sgd: distributed_momentum_sgd.cpp
	mpicxx $(TESTING_FLAGS) $(RPATH_FLAGS) -o ./bin/$@ $< $(INC) -I../include -D_HAS_MPI_ -L$(LIB_PATH) -lmagmadnn $(LIBDIRS) $(LIBS)

clean:
	rm *.o

