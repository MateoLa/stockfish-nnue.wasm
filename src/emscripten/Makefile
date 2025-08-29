# From Hiroshi Ogawa @github.com/hi-ogawa &&
# Mateo Laiño @github.com/mateola
#

EXE = stockfish.js
COMP = em++
CXX = em++
comp = clang
arch = wasm
bits = 64

# Build options
wasm_simd = yes
embedded_nnue = no
minify_js = yes
assertion = no

VPATH := $(VPATH):emscripten
EM_CXXFLAGS += -pthread
EM_LDFLAGS += \
	-s EXPORT_ES6=1 -s ENVIRONMENT=worker -s STRICT=1 \
	-s ASYNCIFY=1 -s 'ASYNCIFY_IMPORTS=["emscripten_utils_getline_impl"]' \
	-s PROXY_TO_PTHREAD=1 \
	-s ALLOW_MEMORY_GROWTH=1 -s INITIAL_MEMORY=$$((1 << 27)) -s MAXIMUM_MEMORY=$$((1 << 31)) \
	-s FORCE_FILESYSTEM -s EXPORTED_RUNTIME_METHODS=["FS"] \
	-s ALLOW_UNIMPLEMENTED_SYSCALLS
# emscripten.org/docs/tools_reference/settings_reference.html

ifeq ($(minify_js),yes)
	EM_LDFLAGS += --closure 1
else
	EM_LDFLAGS += --closure 0 --minify 0
endif

ifeq ($(assertion),yes)
	EM_LDFLAGS += -s ASSERTIONS=1
else
	EM_LDFLAGS += -s ASSERTIONS=0
endif

ifeq ($(wasm_simd),yes)
	EM_CXXFLAGS += -DUSE_WASM_SIMD -msimd128
	SRCS += emscripten/wasm_simd.cpp
endif

ifeq ($(embedded_nnue),yes)
	SRCS += emscripten/embedded_nnue.cpp
else
	EM_CXXFLAGS += -DNNUE_EMBEDDING_OFF
endif

EM_EMSCRIPTEN = $(shell emsdk list | grep INSTALLED | head -n 1 | awk '{ print $$1 }')

EXTRACXXFLAGS += \
	-DUSE_POPCNT $(EM_CXXFLAGS) \
	-DEM_EMSCRIPTEN=$(EM_EMSCRIPTEN)
EXTRALDFLAGS += $(EM_LDFLAGS)

EM_ARTIFACTS += \
	stockfish.js stockfish.wasm stockfish.worker.js \
	emscripten/embedded_nnue.cpp \
	emscripten/public/stockfish.js emscripten/public/stockfish.wasm emscripten/public/stockfish.worker.js \
	emscripten/public/AUTHORS emscripten/public/Copying.txt

ifeq ($(embedded_nnue),yes)
emscripten/embedded_nnue.cpp:
	$(eval nnuenet := $(shell grep EvalFileDefaultNameBig evaluate.h | grep define | sed 's/.*\(nn-[a-z0-9]\{12\}.nnue\).*/\1/'))
	@echo "Big net: $(nnuenet)"
	test $@ -nt $(nnuenet) < $(nnuenet) > $@

	$(eval nnuenet := $(shell grep EvalFileDefaultNameSmall evaluate.h | grep define | sed 's/.*\(nn-[a-z0-9]\{12\}.nnue\).*/\1/'))
	@echo "Small net: $(nnuenet)"
	test $@ -nt $(nnuenet) < $(nnuenet) > $@
endif

emscripten_copy_files:
	cp -f ../AUTHORS ../Copying.txt stockfish.js stockfish.wasm emscripten/public
	cat stockfish.worker.js emscripten/worker-postamble.js > emscripten/public/stockfish.worker.js

emscripten_build: build emscripten_copy_files

# emscripten_clean: clean
# 	@rm -f $(EM_ARTIFACTS)