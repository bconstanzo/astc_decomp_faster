**bconstanzo:** this is a fork of K0lb3's work focused on improving performance by adding some Cython magic (types, which took a bit of effort to get right). Since it completely removes python from the hot loop (and almost all of the decompression function) it achieves some nice speedups (6-8 times faster). Any bottlenecks would then be inside the C++ functions, or your compilers ability to optimize said code.

The main reason I'm making this fork is that iLEAPP had the astc_decomp module as a bottleneck at the time of release, and I worked a couple of weeks into improving it. There may be better alternatives for ASTC decompression out there.

**K0lb3's original readme follows:**

# ASTC_Decomp
An ASTC decoder for PIL.

The decoder uses [richgel999/astc_dec](https://github.com/richgel999/astc_dec) to decompress the ASTC blocks.


## Installation
- Cython required
### PIP
```
pip install astc_decomp
```
### Manual
```cmd
python setup.py install
```


## Usage
### Arguments
* block_width: - Block width, in pixels.
* block_height: - Block height, in pixels.
* is_srgb: - If isSRGB is true, the spec requires the decoder to scale the LDR 8-bit endpoints to 16-bit before interpolation slightly differently, which will lead to different outputs. So be sure to set it correctly (ideally it should match whatever the encoder did).
(optional arg, default : False)

### PIL.Image decoder
```python
from PIL import Image
import astc_decomp 
#needs to be imported once in the active code, so that the codec can register itself

astc_data : bytes
block_width : int
block_height : int
is_srgb : bool = False
img = Image.frombytes('RGBA', size, astc_data, 'astc', (block_width, block_height, is_srgb))
```

### raw decoder
```python
from astc_decomp import decompress_astc

# ASTC to RGBA
rgba_data = decompress_astc(astc_data : bytes, width : int, height : int, block_width : int, block_height : int, is_srgb : bool = False)
```