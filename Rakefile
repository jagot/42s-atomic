# coding: utf-8
require 'yaml'

PROGRAMS = ["lelev", "lconst"]
RAWS = PROGRAMS.map { |p| "build/raw/#{p}.raw" }
puts RAWS

task :default => RAWS

directory "build"

rule ".raw" => [->(f){source_for_raw(f)}, "build"] do |task|
  mkdir_p task.name.pathmap("%d")
  `./txt2raw.pl #{task.source} #{task.name}`
end

task "build/hp42s/lelev.hp42s" => "eVs.txt" do |task|
  mkdir_p task.name.pathmap("%d")
  lines = File.open(task.source).each_line
  File.open(task.name, "w") do |file|
    ["LBL \"LELEV\"",
     lines.count, 1, "NEWMAT",
     "STO \"ELEV\"",
     "INDEX \"ELEV\""].each do |x|
      file.write("#{x}\n")
    end
    lines.rewind
    lines.each do |l|
      [l.strip, "STOEL", "I+"].each do |x|
        file.write("#{x}\n")
      end
    end
    file.write("CLST\n")
    file.write("END")
  end
end

task "build/hp42s/lconst.hp42s" => "constants.yaml" do |task|
  mkdir_p task.name.pathmap("%d")
  constants = File.open(task.source) do |file|
    YAML.load(file.read)
  end
  File.open(task.name, "w") do |file|
    file.write("LBL \"LCONST\"\n")
    constants.each do |k,v|
      # txt2raw.pl requires uppercase E in scientific notation
      ["#{v}".sub("e", "E"),"STO \"#{k}\""].each do |x|
        file.write("#{x}\n")
      end
    end
    file.write("CLST\n")
    file.write("END")
  end
end

def source_for_raw(raw_file)
  raw_file.pathmap("%{^build/raw/,build/hp42s/}X.hp42s")
end
