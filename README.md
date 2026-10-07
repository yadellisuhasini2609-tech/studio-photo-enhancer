const uploadZone = document.getElementById('uploadZone');
const photoInput = document.getElementById('photoInput');
const previewImage = document.getElementById('previewImage');
const imagePreviewWrap = document.getElementById('imagePreviewWrap');
const enhanceBtn = document.getElementById('enhanceBtn');
const resetBtn = document.getElementById('resetBtn');
const presetButtons = document.querySelectorAll('.preset');

const controls = {
  brightness: document.getElementById('brightness'),
  contrast: document.getElementById('contrast'),
  saturation: document.getElementById('saturation'),
  sharpness: document.getElementById('sharpness')
};

const presetMap = {
  natural: {
    brightness: 105,
    contrast: 120,
    saturation: 105,
    sharpness: 35
  },
  studio: {
    brightness: 110,
    contrast: 145,
    saturation: 120,
    sharpness: 60
  },
  passport: {
    brightness: 100,
    contrast: 130,
    saturation: 110,
    sharpness: 40
  }
};

function updateFilters() {
  const brightness = controls.brightness.value;
  const contrast = controls.contrast.value;
  const saturation = controls.saturation.value;
  const sharpness = controls.sharpness.value;

  const filterValue = `brightness(${brightness / 100}) contrast(${contrast / 100}) saturate(${saturation / 100}) sepia(${Math.min(sharpness / 170, 0.15)})`;
  previewImage.style.filter = filterValue;
}

function applyPreset(name) {
  const preset = presetMap[name];
  if (!preset) return;

  Object.entries(preset).forEach(([key, value]) => {
    controls[key].value = value;
  });

  updateFilters();
  presetButtons.forEach((button) => {
    button.classList.toggle('active', button.dataset.preset === name);
  });
}

uploadZone.addEventListener('click', () => photoInput.click());

photoInput.addEventListener('change', (event) => {
  const [file] = event.target.files;
  if (!file) return;

  const url = URL.createObjectURL(file);
  previewImage.src = url;
  imagePreviewWrap.classList.remove('hidden');
  uploadZone.style.display = 'none';
});

Object.values(controls).forEach((control) => {
  control.addEventListener('input', updateFilters);
});

enhanceBtn.addEventListener('click', () => {
  const current = {
    brightness: Number(controls.brightness.value),
    contrast: Number(controls.contrast.value),
    saturation: Number(controls.saturation.value),
    sharpness: Number(controls.sharpness.value)
  };

  controls.brightness.value = Math.min(130, current.brightness + 10);
  controls.contrast.value = Math.min(170, current.contrast + 18);
  controls.saturation.value = Math.min(150, current.saturation + 12);
  controls.sharpness.value = Math.min(100, current.sharpness + 12);

  updateFilters();
});

resetBtn.addEventListener('click', () => {
  applyPreset('natural');
});

presetButtons.forEach((button) => {
  button.addEventListener('click', () => applyPreset(button.dataset.preset));
});

updateFilters();
