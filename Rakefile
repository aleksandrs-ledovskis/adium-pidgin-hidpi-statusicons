BUILD_DIR  = "build/Pidgin Status HiDPI.AdiumStatusIcons"
SOURCE_DIR = "src"

task default: :build

desc "Clean build directory"
task :clean do
  FileUtils.remove_dir(BUILD_DIR, true)
  FileUtils.rm(Dir["build/*.zip"])
end

task :get_version do
  @version = File.read("VERSION").chop
end

task :prepare_build_dir do
  FileUtils.mkdir_p("#{BUILD_DIR}/Resources")
end

task prepare_icons_plist: [:get_version] do
  build_file  = "#{BUILD_DIR}/Icons.plist"
  source_file = "#{SOURCE_DIR}/Icons.plist"

  open(build_file, "w") do |output|
    open(source_file) do |input|
      while (line = input.gets)
        if line =~ /{{VERSION}}/
          output << line.gsub("{{VERSION}}", @version)
        else
          output << line
        end
      end
    end
  end
end

task :create_tiff_files do
  icons = Dir["#{SOURCE_DIR}/*@2x.png"]

  icons.each do |icon_path|
    base_name = File.basename(icon_path).sub("@2x.png", "")

    lowdpi_icon_path = File.absolute_path("#{SOURCE_DIR}/#{base_name}.png")
    hidpi_icon_path  = File.absolute_path(icon_path)
    tiff_icon_path   = File.absolute_path("#{BUILD_DIR}/#{base_name}.tiff")

    `tiffutil -cathidpicheck "#{lowdpi_icon_path}" "#{hidpi_icon_path}" -out "#{tiff_icon_path}" 2>&1 > /dev/null`
  end
end

desc "Build status icon set"
task build: %i(
  prepare_build_dir
  prepare_icons_plist
  create_tiff_files
)

task :copy_license do
  FileUtils.cp("LICENSE", "#{BUILD_DIR}/../LICENSE")
end

task :copy_preview_image do
  FileUtils.cp("#{SOURCE_DIR}/Resources/PreviewImage.png", "#{BUILD_DIR}/Resources/PreviewImage.png")
end

desc "Create zip with built icon set"
task prepare_release: %i(get_version copy_license copy_preview_image) do
  `cd build; zip -x@exclude.lst -r "Pidgin Status HiDPI.AdiumStatusIcons v#{@version}.zip" .`
end
